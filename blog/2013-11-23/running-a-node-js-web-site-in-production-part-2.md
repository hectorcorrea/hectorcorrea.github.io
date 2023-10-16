# Running a Node.js web site in production (part 2)
Last year I blogged about my [first experiences hosting a Node.js website in production](https://hectorcorrea.com/blog/running-a-node-js-web-site-in-production-a-beginners-guide/49). In this post I detail what I have learned in the last 12 months about hosting Node.js applications.


## Finding a Node.js Hosting Provider for Under $100/year
The first thing that I learned is that there are still **no cheap web hosting providers for Node.js** like there are for other programming environments. For example, you can easily find hosting companies for PHP, Ruby, or ASP.NET with support for multiple sites, using your own domain, with file storage included, and database access for under $100 a year. But that's not the case for Node.js.

Although you can find many great hosting companies that support Node.js (Nodejitsu, Heroku, Azure) the price to get a full environment to host many sites, under your domain, with file storage, and database access easily goes into $300 a year. Not too expensive if you are running a business, but more than I wanted to spend for my pet projects.

For a while I hosted my site in Nodejitsu, which is a phenomenal Platform as a Service (PaaS) provider for Node.js. For $9 a month I was able to host one site and have access to a free MongoDB database via MongoLab and I was very happy with it. But Nodejitsu does not provide file storage (e.g. to store my pictures) and the price goes up significantly (to $33 a month) as soon as I want to host more than one site.


## Do It Yourself? Really?
In my post last year I mentioned that it wasn't too hard to host a Node.js site on your own by using a Linux Virtual Machine. Last year I felt uneasy about doing this because I am not an expert Linux administrator and I really wished I could have somebody else take care of the infrastructure for me.

However, given that I have not been able to find a company to do the hosting for me on the cheap (with all the bells and whistles that I want) I decided to take a look again at hosting the site on my own on an Infrastructure as a Service (IaaS) model. The fact that Amazon provides a free Linux Virtual Machine [for a full year](http://aws.amazon.com/free/) made it easy for me to take the plunge and host my sites on my own. This allows me to evaluate this approach for an extended period and reevaluate next year.


## The Big Picture
The following diagram shows how my web site is hosted this time on Amazon's cloud.

![Site on AWS](https://hectorcorrea.com/images/siteonaws.jpg")

The rest of this post will go into details of this diagram.


## Linux Virtual Machine
As you can see in the diagram my site is running on a **Linux Virtual Machine on Amazon EC2** infrastructure. This is a *micro* instance which is what Amazon allows me to use for free. A micro instance has little memory and CPU allocation but it's enough for low traffic sites like mine. After my one-year trial expires the price for this kind of instance is [about $180 a year](http://aws.amazon.com/ec2/pricing/), or $126 if I commit for another year. Not quite under $100 but close enough. 

Installing the tools that I need to get my site up and running in this Linux VM was extremely easy given that there are packages already available to install Node.js, Nginx, and git with a few commands.


## Nginx
Unlike last year where I was using Node.js to listen for all incoming traffic on port 80 this time I am using **Nginx as the front web server**. Nginx provides a lot of system level features (like running as a daemon and with the right security access to run on port 80) so that I don't have to worry about that. This doesn't make me a Linux admin per-se, but it reduces my anxiety level on being the one responsible for handling all traffic coming to port 80. As any system administrator will tell you, there is a lot of unwanted traffic that comes to any public web site on port 80. I sleep much better at night knowing that a battle tested web server like Nginx is handling all that traffic rather than my own Node.js application.

There is a lot of information [on the web](https://www.google.com/search?q=nginx+proxy+for+node) on how to configure Nginx as a proxy for Node.js applications (this is [a good place to start](http://blog.argteam.com/coding/hardening-node-js-for-production-part-2-using-nginx-to-avoid-node-js-load/)) Below are the settings that I am using to configure Nginx to forward port 80 to my Node.js site running on port 3000, notice the `listen` and the `proxy_pass` settings:

```code
server {
  listen    localhost:80;
  location / {
    proxy_cache cache_one;
    proxy_cache_valid  200 304 404 12h;
    proxy_cache_key $host$uri$is_args$args;

    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For  $remote_addr;
    proxy_pass http://localhost:3000/;
    proxy_redirect off;  
    break;
  }
}
```

Nginx has also some great caching features available out of the box. These features allow me to cache the content of my site and reduce the number of requests that my Node.js code has to handle. The `proxy_cache` settings in this script configure Nginx to cache requests. By default Nginx will cache them for as long as the cache-expires on the original response is set in my Node.js code, more on this below.

Another nice thing that Nginx provides out of the box is the ability to host multiple sites through port 80. To do this I just need to add a new server section to the config file and redirect port 80 coming to a specific host to another Node.js application running on another port, say on port 3010.

You can find my complete Nginx configuration files [here](https://github.com/hectorcorrea/hectorcorrea.com/blob/master/etc/nginx/nginx.conf) if you want to see the details. Notice that I've split my config in multiple files, one per site, but all of them are under this folder in GitHub.


## The Node.js Code
The code for my site is a typical Node.js application using Express.js on the server side, MongoDB to store data, and Angular.js on the client side. You can find the [entire source code in this GitHub repo](https://github.com/hectorcorrea/hectorcorrea.com).

Since I want to leverage Nginx for caching, I had to configure Express to return the proper `cache-expire` settings for static assets (like JS and HTML files) and data-driven pages. This is how I configured Express.js to cache static assets for a full day:

```code
var oneDay = 86400000; 
app.use(express.static(viewsPath, {maxAge: oneDay})); 
```

When a request comes for an static file (say an HTML partial view or my client-side app.js file) Nginx will forward the request to my Express.js application. Express will serve the file with a one day expiration value in the HTTP headers. Ngnix will recognize this and cache the file so that next time somebody requests the file in the next 24 hours Nginx will be able to serve it *without having to ask Express.js for it*.

I do the same things for requests that hit the database, I cache them for a few minutes so that if my site were to visited by a very large number of users at the same time, only one of them will need to access the database, the rest will get the cached response directly from Nginx. Below is how I configured the response for any data request to set the proper cache-expire headers:

```code
  var minutes = 5;
  var seconds = minutes * 60;
  var ms = seconds * 1000;
  res.setHeader("Cache-Control", "public, max-age=" + seconds); 
  res.setHeader("Expires", new Date(Date.now() + ms).toUTCString());
  res.send({my json data goes here});
```

I do all my development and testing on my MacAir. Once I am ready to deploy my updates to my Linux VM I `git push` my changes to GitHub, SSH into my Linux VM, and do a `git pull` to update the code there. Although this is a smooth process I would like to eventually develop a process in which I don't have to SSH into my Linux VM to pull the code.


## Database Access
~~Since my Linux VM is a t1.micro instance, I am not running MongoDB on it. Instead I am using a free database hosted at MongoLab. This has the advantage that I don't have to worry about running a database server, but it has the disadvantage that I am making a network request to total different network host to get my data. For as little traffic as I get this is not a problem for me, but it's something to be aware of.~~

**Update Jun/2015:** Recently I installed MongoDB on my little Linux VM and it is working with no problems. At one point I was worried that given the limited memory of my VM (which is a t1.micro) hosting MongoDB on it might put it over the edge but so far so good. Although I was happy having somebody else host the database I was really not happy with having an external dependency for this. Let's see how this works.

Given that I am hosting my site on Amazon cloud, I should eventually play with the idea of using Amazon's DynamoDB to store data rather than MongoDB. Maybe I'll do that one of these days. Yet, I hesitate to use a storage options that is only available with this particular cloud provider.


## File Storage
My Linux VM has about 8GB of disk storage so I could store my pictures and other files on this VM if I wanted, but since Amazon provides a [dirt cheap storage service](http://aws.amazon.com/s3/pricing/) called S3 I am using that instead. Storing my pictures and other large static files in Amazon S3 also frees up my Linux VM from having to serve this traffic. A good solution all around.


## On Building Single Page Applications with Angular.js and Express.js
This is the first time that I build a full Single Page Application (SPA) with Express.js and Angular.js. I will go into more details about my experience on this on a separate blog post, but one of the things that I noticed is that since the views are now rendered client-side **the server becomes a plain REST API**.

For example, when the client request a list of blog posts, the server (Express.js) just needs to return a JSON array with the data and the the client (Angular.js) renders it. This is something that *should happen* naturally on any application that uses an MVC framework on the server-side (like Express) but that it is *more likely to happen* when building SPAs because the client-side framework (Angular.js in my case) can handle a whole lot more UI logic.

Below is the entire code in one of my server-side controllers to return a list of blog entries, notice how small it is and the absence of user interface logic.

```code
exports.all = function(req, res) {

  var m = model.blog(req.app.settings.config.dbUrl);
  var includeDrafts = req.isAuth;
  m.getAll(includeDrafts, function(err, documents){

    if(err) {
      return error(req, res, "Cannot retrieve all blog entries", err);
    }

    var blogs = docsToJson(documents);
    req.app.settings.setCache(res, 5);
    res.send(blogs);
  });

};
```

Given that the server-side becomes just an API I am also reconsidering the use of Express.js. Perhaps just using Connect.js would be enough with this architecture. I am not sure about this just yet but something to research into.

There are some complications that come when you deal with this SPA architecture, though. For example, how do you handle devices that don't support JavaScript? Also, there are specific considerations to allow search engine to crawl your site. Again, I'll address these issues in more detail in a separate post.


## Summary
~~I've been running my site with this architecture for about a month now and I am pretty happy with it. Some of my initial concerns about having to deal with the infrastructure have been alleviated by using Nginx as the front web server. I still need to research more on how often I should be updating my Linux installation with patches and such, but so far it has not been been a problem.~~

~~As far as the price is concerned I couldn't be happier. Right now I am paying $0.00 dollars for this because I am using my one-year free trial. I suspect I will end up paying about $126 next year once my free trial expires. Also, as long as MongoLab lets me host my MongoDB database for free I am perfectly happy with them.~~

**Update Jun/2015:** It's been over a year since I originally posted this blog entry. I ended up staying with this setup and I've paying about $9.50 a month for hosting. Not quite under $100/year but not bad either. I really like the ability to have a full Linux box at my disposal and I am enjoying the IaaS model more than I thought I would. For example, a few months ago I wanted to rebuild my Linux VM and I was able to spin a separate VM, get it up and running with my setup in a matter of hours, and then shutdown the old machine. Since I had two machines running for a few hours I had to pay extra for that time but given that the cost is about $0.013 per hour the total "extra" amount that I paid was not even 50 cents. 

**Update Dec/2015:** I recently switched from hosting my Linux VM from Amazon AWS to Digital Ocean. At the moment I am using a very small machine that has similar specs to my micro AWS instance and I am paying $5/month. The next level is $10/month (with 1 GB of RAM) that sounds very appealing. I love that the pricing in Digital Ocean is rather clear. One of the advantages to using an Infrastructure as a Service (IaaS) provider is that is very easy to switch from one provider to the next, something that is much harder if you use a Platform as a Service (PaaS) provider.

**Update Jan/2017:** I've rewritten my site in Go and described my experience [in this other post](https://hectorcorrea.com/blog/migrating-my-site-from-node-js-to-go/69). Check it out. 
