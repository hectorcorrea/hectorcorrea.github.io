# Introduction to Node.js
[Node.js](http://nodejs.org) is a JavaScript interpreter that runs in a server environment and gives us the ability to run JavaScript code directly on the server (i.e. outside of a browser) just like we can run server side code in other languages like PHP, Python, Ruby, C#, or Java.

Node.js is based on the JavaScript engine that Google wrote and uses for the Chrome browser. The author of Node.js, Ryan Dahl, took this JavaScript engine (called V8) and [repurposed it so that it can run in a server environment](http://www.ibm.com/developerworks/opensource/library/os-nodejs/). Ryan also added some features that are important when running on a server, for example, the ability to read and write from the file system and the ability to receive and send HTTP requests.  

Node.js is open source and there are installers available for Mac, Windows, and Linux. The installer for Mac is under 8MB in size. You can download and install the proper installer for your platform from [here](http://nodejs.org/). Once you have Node.js installed you should be able to run it from your terminal window as follows:

```terminal
$ node --version
v0.8.17
$
```

**Note:** All of the code examples that I present in this blog post are available at [this github repo](https://github.com/hectorcorrea/intro-to-nodejs). Feel free to download them and play with them.
 

## Hello World
The fact that Node.js is a JavaScript interpreter that runs on the server means that we can write a *helloworld.js* program like the one below:

```code
console.log("hello world");

var a = ['batman', 'robin'];
a.push('superman');
console.log(a);

var employee = {name: "jane developer", department: "IT" };
console.log(employee); 
```

and execute it from the Terminal by running `node helloworld.js`

```terminal
$ node helloworld.js
hello world
[ 'batman', 'robin', 'superman' ]
{ name: 'jane developer', department: 'IT' }
$
```

The previous example is rather silly but it serves to demonstrate that the code that we write when using Node.js is typical JavaScript code, notice how the array and the employee object were declared using just plain JavaScript constructs. All the code that we write for Node.js will use traditional JavaScript syntax for features like variable scoping, types, function declarations, statements available for loops and conditionals, et cetera.


## Hello World - Web Version

The goal of this blog post is to show how to use Node.js for web development, so let's write a simple program to process web requests. The following program is a web server that will return "hello world" to the browser for any request to `http://localhost:3000`

```code
var http = require("http");
var port = 3000;
var serverUrl = "localhost";

var server = http.createServer(function(req, res) {

  console.log("Request: " + req.url);
  
  var now = new Date();
  var html = "&lt;p&gt;Hello World, the time is " + now + ".&lt;/p&gt;";
  res.end(html);

});

console.log("Listening at " + serverUrl + ":" + port);
server.listen(port, serverUrl);
```

If we save this program as *webserver1.js* we could run it from the console with `node webserver1.js`. If we launch our browser and point it to http://localhost:3000/somepage.html we should see something like this:

```terminal
$node webserver1.js
Listening at localhost:3000
Request: /somepage.html
```

Notice that with just 16 lines of JavaScript code we were able to stand up a web server and start listening for HTTP requests. Keep in mind that this is a standalone web server, there is no Apache, nginx, or IIS running behind the scenes. Just 16 lines of JavaScript (plus having Node.js installed) and we are listening and processing HTTP requests! 

If you are comfortable with JavaScript the code in lines 5 through 13 might look familiar to you (even if you have never used Node.js.) In line 5 we create a server object and pass a callback function to execute every time an HTTP request is received. When executed, this callback function will receive two parameters, one will have a reference of the HTTP *request* that was received and the other will be a reference to the HTTP *response* that will be sent to the client/browser.

However, *if you are not familiar with JavaScript this code might look rather strange*. The code in lines 5 through 13 passes a function as an argument to `createServer`. This is similar in concept to [delegates in C#](https://msdn.microsoft.com/en-us/library/ms173171.aspx)  or [blocks in Ruby](http://www.robertsosinski.com/2008/12/21/understanding-ruby-blocks-procs-and-lambdas/). The important thing to notice is that we are passing *a function as a parameter* to `createServer`. This function will be called internally by the server object when an HTTP request is received. Notice that we are passing the function as a parameter, not the result of executing the function.

The last line of this program calls the `server.listen()` method to start listening for HTTP requests. Internally the listen() method will start a loop waiting for HTTP requests. Every time an HTTP request is received it will call our callback function which displays to the console the URL of the request that was received and returns to the browser an HTML paragraph with the text "hello world". The program will stay on this loop until you cancel the program with CTRL+C from the terminal.

The full code for this example can be found under [webserver1.js](https://github.com/hectorcorrea/intro-to-nodejs/blob/master/webserver1.js) in the github repo for this blog post.


## Event-driven, non-blocking I/O

One of the key differences between Node.js and other server platforms is the fact that it uses an event-driven, non-blocking I/O model to process requests. This is such a key and important feature that is embedded on the opening paragraph in the [Node.js home page](http://nodejs.org):

> Node.js is a platform built on Chrome's JavaScript runtime 
> for easily building fast, scalable network applications. 
> Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient, 
> perfect for data-intensive real-time applications that run across distributed devices. 

**Event-driven** means that when a Node.js program executes it goes into a loop and process events as they come. This is what happens in our previous program. The line server.listen() puts Node.js in a loop waiting for HTTP requests to come through. Every time an HTTP request is detected an event is fired to process it, during this process our callback function will be called to give us a chance to act on this request. In our case we read the URL that came on the request, output it to the console, and return "hello world" on the response to the browser. A more accurate explanation of this process can be found on the [About page on the Node.js web site](http://nodejs.org/about/). This *event-driven model* is different from a traditional *threaded model* used by other web servers like Apache and IIS. Node.js is not the only tool that uses an event-driven model, Nginx is another well-known tool that uses this approach. The details of the differences between the even-driven and threaded models are beyond the scope of this post.

**Non-blocking I/O** is another key feature of Node.js. This means that (mostly) all I/O is performed with non-blocking functions. This is different from most server side programming environments in which I/O is performed using blocking code. The following pseudo-code shows an example of reading a text file with a blocking function and with a non-blocking function.


```code
// Blocking implementation
var text = readFileSync("sample.html")
console.log(text); // executed after the file has been read completely
```

```code
// Non-blocking implementation
readFile("sample.html", function(err, text) {
    console.log(text); // executed after the file has been read completely 
});
console.log("xxx"); // this line will be executed immediately
```

The **blocking** example is very similar to the way we perform I/O nowadays in most programming environments. We call a function to read a file and once the file has been read we display its content to the console. This is called a blocking implementation because the thread where this code is executing is blocked until the file has been read completely. Once the file has been read then the execution continues and, in our case, we output the contents to the console.

The **non-blocking** example shows the way I/O is performed in Node.js. In this case we read a file but we don't block the thread of execution, instead, we the tell the system to continue its execution and call our callback function once the file has been read completely. In this example the line "xxx" will be displayed in the console immediately and the text of the document will be displayed later on (which could be a few milliseconds later or a few seconds later.) The important thing to notice is that *the thread of execution was not blocked while the file was being read*, which is why "xxx" was displayed immediately.

A more thorough explanation of blocking vs non-blocking I/O as well as the advantages and disadvantages of each approach is beyond the scope of this post but these are two good blog posts to check to learn more about this: [Understanding the node.js event loop](http://blog.mixu.net/2011/02/01/understanding-the-node-js-event-loop/) and [The main issue with non-blocking servers](http://amix.dk/blog/post/19581). Also, on the video [Ryan Dahl - History of Node.js](http://www.youtube.com/watch?v=SAc0vQCC6UQ&feature=related) the author of Node.js gives a good explanation why he chose this model when he developed Node.js (between minutes 15 and 20).

The important thing to notice is that Node.js uses an event-driven and non-blocking I/O model and you need to become familiar with this programming model. This is probably the biggest challenge to get started with Node.js because most of us are not used to work in such environment.

Below is an updated version of the simple web server example that we saw in the previous section but this time with the ability to read a particular file from disk using Node.js' `readFile` function for non-blocking I/O:

```code
var http = require("http");
var fs = require('fs');
var port = 3000;
var serverUrl = "127.0.0.1";
var counter = 0;

var server = http.createServer(function(req, res) {

  counter++;
  console.log("Request: " + req.url + " (" + counter + ")");
  
  if(req.url == "/sample.html") {

    fs.readFile("sample.html", function(err, text){
      res.setHeader("Content-Type", "text/html");
      res.end(text);
    });
    return;

  }

  res.setHeader("Content-Type", "text/html");
  res.end("&lt;p&gt;Hello World. Request counter: " + counter + ".&lt;/p&gt;");

});

console.log("Starting web server at " + serverUrl + ":" + port);
server.listen(port, serverUrl);
```

In this example, when a request is made for file "sample.html" we read this file from disk and return its content to the browser. For any other request we return the text "hello world" as we did before.

Notice that in line 14 we read file "sample.html" using the asynchronous `readFile` function and immediately after (on line 18) we exit this function, which effectively gives the control back to Node.js' event loop to process more requests (or sits idle if there is no new requests to process.) When the file has been read (either a few milliseconds later or a few seconds later) Node will call our callback function (lines 14-17) and give us the content of the file that was read, at that point we'll build a response and return it to the browser.

The full code for this example can be found under [webserver2.js](https://github.com/hectorcorrea/intro-to-nodejs/blob/master/webserver2.js) in the github repo for this blog post.


## The Node.js Ecosystem

When you install Node.js in your system also get a tool called Node Package Manager (npm). NPM is a tool that you can use to download and install libraries (packages) developed for Node.js. You can get an idea of the list of tools available for node by visiting the npm web site at [https://npmjs.org](https://npmjs.org). There are more than 20 thousand packages as of this writing.

With npm you can download tools for debugging Node.js applications, MVC frameworks, database drivers, unit testing tools, libraries for asynchronous functions, tools minification and concatenation of scripts, CoffeeScript compiler, et cetera.

To download a particular tool you just have to run `npm install name-of-package` from the folder where you application lives. This will create a `node_modules` subfolder and store in the source code of the package that you installed.


## Hello World with Express.js

One of the most popular frameworks to build web sites with Node.js is called Express.js. [Express.js](http://expressjs.com) is an MVC-framework similar to Sinatra (for Ruby) or ASP.NET MVC (for C#) and it provides a lot of the plumbing required to parse HTTP requests (including URL parameters, query string, HTML forms, cookies), handle URL routing, and support for view engines (or templates.) Unlike Ruby on Rails, Express.js does not provide any built-in support for models.

> Express is a minimal and flexible node.js web application framework, 
> providing a robust set of features for building single and multi-page, 
> and hybrid web applications. -- http://expressjs.com

You can use npm to install Express in your machine with the following steps

```terminal
$ mkdir ~/dev/myapp
$ cd ~/dev/myapp
$ npm install express
[a bunch of text will show up here]
$ 
```

While we are at it, let's install a separate module to allow us to log HTTP requests to the console and see what the browser sends to the server.

```terminal
$ npm install morgan
[a short text will show up here]
$ 
```

Once Express and Morgan have been installed create a file called *express1.js* with the following content:

```code
var path = require('path');
var express = require('express');
var logger = require('morgan');
var app = express();

// Log the requests
app.use(logger('dev'));

// Serve static files
app.use(express.static(path.join(__dirname, 'public'))); 

// Route for everything else.
app.get('*', function(req, res){
  res.send('Hello World');
});

// Fire it up!
app.listen(3000);
console.log('Listening on port 3000');
```

On line 2 we load the Express module and assign it to a variable called `express` and then use this variable to create an express application `app` in the next line. This `app` variable is similar to the `server` variable in the *hello world* example in the previous section except that `app` has a whole set of new features provided by the Express framework which we will discuss next.

Line 6 sets up the *request logger* so that Express outputs to the console any requests that it processes. 

Line 9 sets up a middleware to handle *static files* found in a subfolder called `public` (you can name it whatever you want.) It's usually a good idea to dump to this folder static assets like HTML/CSS files, client side JavaScript libraries (like jQuery), and images that you need for your web sites but that don't change very often.

Lines 12-14 sets up a custom *middleware* for any request that does not correspond to a static file. We'll elaborate on this in a second.

Line 17 *starts listening for requests* coming on port 3000. Very similar to the way our *hello world* application did in the previous section.

To test this application you just need to run `node express1.js` from the Terminal and browse to `http://localhost:3000`, then to `http://localhost:3000/whatever` and see what happens. The output in the console should be similar to this:

```terminal
$ node express1.js
Listening on port 3000
GET / 200 31ms - 11
GET /whatever 200 13ms - 11
```

If you create a dummy "sample.html" file and save it inside the public subfolder in your app then you should be able to browse to http://localhost:3000/sample.html and see the contents of this file (notice that you don't need to include public on the URL.)

```terminal
$ mkdir public
$ echo "This is a sample HTML file" &gt; public/sample.html
$ node express1.js
Listening on port 3000
GET / 200 31ms - 11
GET /whatever 200 13ms - 11
GET /sample.html 200 13ms - 25
```

When we run `node express1.js` Node.js creates an Express application (lines 2-3), register that all static files will be handled with the built-in `express.static` middleware (line 9), and register that all other requests will be handled with our own custom middleware `app.get('*',...)` (lines 12-14), and finally starts listening for requests on localhost port 3000 (line 17).

When an HTTP request comes through, Express will automatically pass it on to the `express.static` middleware if it is for an existing file inside our public subfolder or let our custom `app.get('*',...)` middleware handle it.

> What is middleware?
> In Express.js parlance middleware is a particular piece of code 
> that is used to handle requests. In our previous example 
> we used the built-in express.static middleware to handle requests for static files 
> and wrote own custom middleware to handle anything else. 
> Our custom middleware just returned "hello world" but as we'll see 
> in the next section this piece of code can do much more.
> -- http://expressjs.com

The full code for this example can be found under [express1.js](https://github.com/hectorcorrea/intro-to-nodejs/blob/master/express1.js) in the github repo for this blog post.


## Express.js and Routes

One of the nice things that Express.js provides out of the box is the ability to automatically route traffic to different middleware depending on how the URL looks like. For example, we can ask Express to use one middleware to handle requests that look like `/books/:id` and a different middleware for requests that look like `/books`. Below is an example of how to configure Express.js to support these two requirements. Be aware that the order in which the routes are defined is very important, make sure you put the code for the new routes in between the static handler and the wildcard `app.get('*',...)` handler:

```code
// Serve static files
app.use(express.static(path.join(__dirname, 'public'))); 

// NEW: Handle requests for a single book
app.get('/books/:id', function(req, res){
  res.send('The details of book ' + req.params.id + ' should go here');
});

// NEW: Handle request for a list of all books
app.get('/books', function(req, res){
  res.send('A list of books should go here');
});

// Route for everything else.
app.get('*', function(req, res){
  res.send('Hello World');
});
```

Notice how in the middleware for `/books/:id` we are accessing the value of the id that was passed on the URL directly as a field on the req.params object. If we browse to `/books/4` the value of `req.params.id` will be `4`.

Another nice thing that Express.js provides is the ability to configure different middleware for different HTTP verbs. For example is possible to define one middleware to handle when users browse to a `/search` page (typically an HTTP GET request) and a different middleware when users post a specific search value (typically an HTTP POST request.) We could just simply define two new routes as shown below, notice how the first one uses `app.get` and the second uses `app.post`:

```code
app.get('/search', function(req, res){
  res.send('Display a search HTML form here');
});

app.post('/search', function(req, res){
  res.send('Your search for ' + req.body.searchText + ' returned X results');
});
```

The full code for this example can be found under [express2.js](https://github.com/hectorcorrea/intro-to-nodejs/blob/master/express2.js) in the github repo for this blog post.


## Express.js and Templates

Express also provides support for several templates (or view engines) to produce HTML without having to do string concatenation as we've been doing in all the previous examples. Two of the most common template engines available for Express.js are EJS and Jade. They both allow you to keep your views in separate files and inject some data elements to them at runtime but EJS uses a syntax that is very similar to HTML while Jade uses a very different syntax.

In this blog post I will use the [EJS template engine](https://github.com/visionmedia/ejs). You can install it on your system as follows:

```terminal
npm install ejs
```

Now let's create a very simple EJS view with the following code and save it as *bookView.ejs*:

```code
<h1>Book Details</h1>
<p><b>Title:</b> <%= book.title %></p>
<p><b>Author:</b> <%= book.author %></p>
```

As you can see in this example, views in EJS use plain old HTML syntax but you can embed JavaScript code by specifying it in between the `<%=` and `%>` symbols. In our example, the value of a property called `book.title` and another called `book.author` will be inserted on this view when it's rendered.

To render this view in our example, let's update the middleware that we used to handle requests for a single book in our previous example with the following:
 
```code
// NEW: Handle requests for a single book
app.get('/books/:id', function(req, res) {
  var bookData = {title: "the name of the book", author: "some writer"};
  res.render('bookView.ejs', {book: bookData});
});
```

Notice how we are calling the `res.render()` method and passing it both, the name of the view to render (viewBook.ejs) and the data that we want to render.

The full code for this example can be found under [express3.js](https://github.com/hectorcorrea/intro-to-nodejs/blob/master/express3.js) in the github repo for this blog post.

**Update December/2014:** All of the Express examples on GitHub for this blog post have been updated to work with Express 4.x


## In Closing

There is a whole lot more to Node.js and frameworks like Express.js, but I hope this blog post gives you an idea of the flexibility of the Node.js platform and libraries available for it. Happy coding!