# Log4net Thread-Safe but not Process-Safe
A few days ago I was struggling with a problem with [log4net](http://logging.apache.org/log4net/index.html) in a web application. I have used log4net in many applications before with little or no problem but in this case even with a single user hitting the web site I was seeing strange logging behaviors. In my case **some of the log entries were being logged but others were not**.

The application in question is a C# ASP.NET MVC web application running under IIS. I am using the true and tried `RollingFileAppender`. It couldn't have been a more typical use of log4net and yet I was seeing a weird things. Here are the symptoms that I was seeing:

* Log4net was logging some entries but not others. 
* The issue was happening even when I was the only user on the site.
* The issue was only happening in one of our development servers but not on my local box. 

Since some entries were being logged but not others I was able to rule out the possibility that IIS might not have enough rights to write to the folder configured for the `RollingFileAppender`. This is a typical issue but not applicable in my case since if IIS wouldn't have enough rights nothing would be logged at all. In addition since the issue was happening even when I was the only user on the site I was sure it was not due to heavy utilization or some sort of race condition (which log4net typically handles nicely anyway.)

The fact that the issue was happening only on the development server with IIS 6 and not on my local machine with IIS 7 made me suspect that the different versions of IIS might the culprit but wasn't sure about that.


## Turning log4net internal diagnostic

To begin troubleshooting I turned on log4net's diagnostic option to see if log4net was itself reporting problems that would give me a hint. If you are not familiar with this log4net feature check out the "How do I enable log4net internal debugging?" section in the [log4net FAQ](http://logging.apache.org/log4net/release/faq.html).

In my case I configured log4net to dump all of its diagnostic information to a file called `LogDiag.txt`. After running my application and hitting the site a few times I went to see what was being logged in the `LogDiag.txt` file and lo and behold I noticed that there were *three LogDiag.txt files* being generated. The following picture shows how this looked on my machine:

![Log4net diagnostic](https://hectorcorrea.com/images/log4netdiag.jpg)

I opened the `LogDiag.txt` file and there were no errors indicated logged in there. However, the other two files (the ones prefixed with a GUID) had the following error:

```terminal
log4net:ERROR [RollingFileAppender] 
   Unable to acquire lock on file c:\dev\SandboxMvc\SandboxMvc\App_Data\LogFile. 
   The process cannot access the file 'c:\dev\SandboxMvc\SandboxMvc\App_Data\LogFile' 
   because it is being used by another process.
log4net:ERROR [RollingFileAppender] 
   OpenFile(c:\dev\SandboxMvc\SandboxMvc\App_Data\LogFile,True) call failed.
```

## Houston, we have a problem...

OK so we indeed have a problem. Log4net is clearly telling me that it is having problems writing to the log file. That's good. At least I am not going crazy. But how come? How come am I having this problem now but not in other applications? How come it happens only on the web server but not in my local box?

The answer turned out to be the way IIS was configured on the server where the problem was happening. It turns out that IIS on that particular box is configured to use what is known as a **web garden** which means that IIS will launch *multiple worker processes* to handle requests. That was different from the configuration on my local box where IIS was setup to have a single worker process running.

The following picture shows how to configure IIS to use one or many worker processes. In this picture you can see that the "ASP.NET 4.0 Application Pool" is configured to use a maximum of three worker processes.

![IIS Maximum Worker Processes Configuration](https://hectorcorrea.com/images/IisWorkerProcesses.JPG)

When the IIS application pool is configured to use more than one worker process (i.e. a web garden) what happens is that multiple processes will be spun by IIS to serve requests for your application. If you configured log4net to use a single file to log requests (as I did) then this means that multiple processes will attempt to write to a single file and locking issues will arise. Once I realized what the problem was finding a solution was quite easy.

The fact that there were three worker process running also explained why I saw three `LogDiag.txt` files on my machine. Log4net was smart enough to name each file differently (remember that two of them were prefixed with a GUID) so that it's internal logging didn't experience the same problem that I was having.

Before I discuss how you can work around this issue let me clarify that there is nothing wrong per-se with having a web garden in IIS. This configuration is actually good on a server-class machine. The discussion on when and how to configure a web garden is beyond the scope of this blog post however the lesson learned is that *if you have a web garden you need to take this into account when configuring the RollingFileAppender*.


## How to address the issue

There are two ways that we can configure the `RollingFileAppender` to work in a web garden. One option is to configure it to use what is known as [minimal lock](http://logging.apache.org/log4net/release/sdk/log4net.Appender.FileAppender.LockingModel.html) on the log file. With this option log4net will only lock the file for the duration of each log operation. This option reduces the chances that more than one process will collide as they keep the file locked to a minimum. However, there is no guarantee that under load they might actually collide. Additionally there is a performance penalty in opening and closing the file on each log entry. I am not sure if the penalty is too high but it's worth testing if you decide to use this option. Below is an example of how to configure the `RollingFileAppender` to use minimal locking (notice the `lockingModel` setting in line 2.)


```code
<appender name="LogFileAppender" type="log4net.Appender.RollingFileAppender,log4net">
  <lockingModel type="log4net.Appender.FileAppender+MinimalLock" />
  <param name="File" value=".\\App_Data\\LogFile" />
  <param name="AppendToFile" value="true" />
  <datePattern value=".yyyy-MM-dd'.txt'" />
  <rollingStyle value="Composite" />
  <maxSizeRollBackups value="5" />
  <maximumFileSize value="100KB" />
  <staticLogFileName value="true" />
  <layout type="log4net.Layout.PatternLayout">
    <conversionPattern value="[LOGENTRY] %date %-5level %logger{2} - %message %newline" />
  <layout>
<appender>
```

The second option to take care of the problem is to **make sure that each IIS worker process writes to its own file** so that there are no collisions at all. With this option if the web garden is configured to use three worker processes then you'll have three simultaneous log files (one for each active worker process.) Each of them can use an exclusive lock on the file as these files are not shared across processes. This method is guaranteed not to have collisions and allows you to keep using the fast exclusive lock method. The disadvantage is that now you have multiple log files to look at rather than a single consolidated one. Below is an example on how to configure the `RollingFileAppender` to create one file for each process. Notice how line 2 now has been configured to use a `PatternString` to name the file and the inclusion of pattern `%processid` to make each file unique by each process ID.

```code
<appender name="LogFileAppender" type="log4net.Appender.RollingFileAppender,log4net">
  <file type="log4net.Util.PatternString" value=".\\App_Data\\Log[%processid]" />
  <param name="AppendToFile" value="true" />
  <datePattern value=".yyyy-MM-dd'.txt'" />
  <rollingStyle value="Composite" />
  <maxSizeRollBackups value="5" />
  <maximumFileSize value="100KB" />
  <staticLogFileName value="true" />
  <layout type="log4net.Layout.PatternLayout">
    <conversionPattern value="[LOGENTRY] %date %-5level %logger{2} - %message %newline" />
  <layout>
<appender>
```


## Another reason for this problem...
**Update Nov/2012** A few months ago a reader of this blog alerted me than in addition to the web garden scenario described in this blog post the issue described here also happens when IIS "Disable Overlapped Recycle" is set to false (which is the default value.) As the reader noted:

> When an application pool restarts, there is a brief period of time 
> where typically multiple w3wp processes are serving the same application, even if web gardens are disabled.  
> In some cases, where there are a large number of outstanding requests, the period may be longer than brief. 
> 
> This would manifest the same symptom as you described. -- Greg Askew

The following screenshot shows this setting in IIS's Default Application Pool:

![Disable Overlapped Recycle](https://hectorcorrea.com/images/iis_disableoverlappedrecycle.png) 


## But I thought log4net was thread-safe?

Log4net is indeed thread-safe as it is clearly indicated on log4net's [FAQ](http://logging.apache.org/log4net/release/faq.html). A lot of people get confused on this because the documentation for the [RollingFileAppender](http://logging.apache.org/log4net/release/sdk/log4net.Appender.RollingFileAppender.html) says that the *appender* is not thread safe. In reality the log4net code takes this into account (see [here](http://stackoverflow.com/questions/1294668/log4net-fileappender-not-thread-safe/1294842#1294842) and [here](http://stackoverflow.com/questions/4098409/thread-safety-of-log4net/4102027#4102027)) and makes sure logging is thread-safe. Some people have even run [tests to prove this](http://stackoverflow.com/questions/1519211/multithread-safe-logging/1520449#1520449).

** Update Dec/2014:** Apparently some people have found that log4net can be "deadlock-prone" and they have logged an [issue about this](https://issues.apache.org/jira/browse/LOG4NET-431). Thanks John Zabroski for the heads up!

However, the fact that log4net is *thread-safe* does not mean that is *process-safe* to write to a single file. This is not log4net's fault, there is no such thing as the ability to have multiple processes write concurrently to the same text file. We use relational databases (not text files) when we need to have multiple processes write to a single repository of data.


## In summary

If you are using log4net's `RollingFileAppender` in an environment where IIS is configured to use multiple worker processes (i.e. a web garden) you must ensure that the `RollingFileAppender` uses one file per worker process rather than a single file. 