# Migrating my site from Node.js to Go
Over the years I have written the code to power my website (hectorcorrea.com) in a variety of programming languages and platforms. The last version of the site was a Node.js application using MongoDB to store the data, the version before that was a C# ASP.NET MVC application with a SQL Server backend. One of the things that I liked the most about Node.js is how lightweight it was compared to a C# website. When I started to learn Go I was surprised how lightweight the language felt while at the same time being incredibly powerful so **I decided to rewrite this site in Go to compare the experience with the Node.js implementation that I had written before**.

I first became interested in Go after reading Rob Pike's post [Less is exponentially more](https://commandcenter.blogspot.com/2012/06/less-is-exponentially-more.html) in which he tells the story on how he, Robert Griesemer, and Ken Thompson were disappointed on how many more features were going to be added to the C++ language. Instead of going with the flow they decided to create **a new programming language with less features** but with enough power to develop serious applications (e.g. "big programs, written and maintained by big teams"). At the time I felt the same way about the C# and Rails ecosystems that seem to thrive in adding more and more features to the platform to the point that you need a multi-gigabyte IDE to work in C# or tons of gems to do anything useful in Ruby.

During my career I have worked with statically typed languages (mainly C#) as well as dynamically typed ones (mostly Ruby and Node.js.) Go strikes me as a language that, although it's statically typed, it feels as lightweight as dynamically typed languages tend to. The fact that it comes with a superb standard library means that you can get going with the language as-is and without having to download a bunch of utilities to build a basic application.

At the beginning I was a bit unsure how it will feel to work in a modern language that decided against some of basic language features that most languages provide nowadays like class inheritance, generics, and exception handling. Instead, Go provides alternative approaches for code re-utilization, message passing, and error handling that, although feel rudimentary at the beginning, work quite well in practice.


## Source code structure

The Go source code for my site is available in GitHub under [this repository](https://github.com/hectorcorrea/hectorcorrea.com) if you are interested. I make no promises about the quality of the code of this project, and as a matter of fact, you should know that this is the first Go application that I deploy to production.

The structure of the code is rather simple:

* File [main.go](https://github.com/hectorcorrea/hectorcorrea.com/blob/go_version/main.go) is the launcher.
* The code to deal with all the HTTP interactions is in the `web` package, you'll see in there the code to handle the requests by type (e.g. requests for static files vs request to handle the dynamic blog pages of the site). File [web\web.go](https://github.com/hectorcorrea/hectorcorrea.com/blob/go_version/web/web.go) is a good place to start.
* The code to handle database access is under the `models` package. For example, [models\blog.go](https://github.com/hectorcorrea/hectorcorrea.com/blob/go_version/models/blog.go) handles the CRUD operations for blog entries.
* Code to handle HTML generation is under the [views](https://github.com/hectorcorrea/hectorcorrea.com/blob/go_version/views) package. Code in the [viewModels](https://github.com/hectorcorrea/hectorcorrea.com/blob/go_version/viewModels) is used to create an object that can be used on each of the views with all the information that the view could possible need. This makes sure the code in the views has a little logic as possible.

As I wrote the code I noticed how similar the structure was to the Node.js code that I had written before, [available on this branch](https://github.com/hectorcorrea/hectorcorrea.com/tree/byenodejs).

In the Node.js version of the site I was using Express.js to handle URL routing and HTTP sessions, whereas in the Go version I decided to write that code myself just for the fun of it. I know there are really good packages out there that simplify this for Go applications but I wanted to see how much I could get done without relying on an external package. My rudimentary router is under [web\routes.go](https://github.com/hectorcorrea/hectorcorrea.com/blob/go_version/web/routes.go) and the session management under [web\session.go](https://github.com/hectorcorrea/hectorcorrea.com/blob/go_version/web/session.go).

The one external package that I do use is the `github.com/go-sql-driver/mysql` to provide access to MySQL. This is where I drew the line :) I was not going to write that code.

However, I did write the SQL statements to access the database. I am not using any Object-Relational-Mapper (ORM), just good old SQL statements written by hand.


## What I like about Go

The Go standard library is rather comprehensive. There are packages/functions for pretty much anything you could think of. There is a rock solid built-in web server, a template engine, crypto utilities, IO handling, string manipulation, XML/JSON generation, SQL database access. You name it, it's there.

The Go toolkit is also very nice, `go fmt` to format your code and `go test` to run unit tests are two of my favorites.

I love that Go is picky and minimalist about its syntax. An `if` statement must have curly braces, even if the body is only one line of code. Likewise, there is only one keyword for loops (`for`).

The fact that Go produces binaries is such a great feature. I can compile a Linux binary from my Mac and deploy it to production by just copying the binary. No need to `git pull` or `bundle update` at the time of deployment. My deployment script is something along the lines of:

```terminal
env GOOS=linux go build  
scp mybinary user@server:/some/path/
```

Contrary to what most people say, I actually like that Go did not provide a tool to handle dependencies the same way `Bundler`  does for Ruby and `npm` does for Node.js. Go provides `go get` to fetch external dependencies but it is a rather crude mechanism that has drawn a lot of criticism from the community. I personally don't care much about this part of the language. I am one of those crazy developers that thinks that  your program should (1) have as few external dependencies as possible and (2) that once you have an external dependency "you own it" and its source code should be in your source  code tree along with the rest of your code. I have found that in order to maintain a code base for a long time (say 5-10 years after its deployment) you need to have the source code of your dependencies along with the rest of your code base. Go sort of forces this convention by not providing a more sophisticated tool and I am OK with it.

I have mixed feelings about error handling in Go. I am not totally in love with the `if err != nil` pattern in Go code, but understand the value of considering errors all throughout the code and making them visible as much as the so called "happy path" of your application. This is particularly true for backend applications and your Go code is backend code when you are writing a website.

In my tiny little site I am not using channels explicitly or other "advanced" concurrency management features of Go. The web server does use goroutines internally to handle concurrent requests, though. But the code looks and feel synchronous from the perspective of the developer. I also appreciate not having to write asynchronous code for database access like one needs to do in Node.js.


## What I don't like about Go

I am still relatively new to Go but there are a few things that I don't quite like about the language and its tooling. Perhaps the biggest one is the lack of a REPL and a debugger. I understand you can install one via some packages but the list of steps to (manually) get a debugger working on a Mac is [intimidating](https://github.com/derekparker/delve/blob/master/Documentation/installation/osx/install.md).

Another thing that I don't like about Go is that, true to its C roots, I feel like I needed to know more about passing by reference vs passing by value when writing Go code than with other languages. I am not sure why this is the case, the concept of passing by value/reference applies just the same to most languages but I have been bitten more than once in Go for passing a value (rather than a reference) in ways than I don't remember in my C# and Ruby code. See [Go is pass-by-value — but it might not always feel like it](https://neilalexander.dev/2021/08/29/go-pass-by-value.html) by Neil Alexander for more information on this.

There are no constructors in Go like there are in C# or Ruby. This is something that I still miss. There is a beauty in forcing the execution of some code to initialize an object no matter how it is created and you cannot do this in Go. In Go objects are initialized with their default values (e.g. empty string for strings, zero for numbers.)


## Overall

Overall I am very pleased with my experience writing my first web application in Go. My other experience with Go was writing a small [LDP Server](https://github.com/hectorcorrea/ldpserver), something that is arguably a better fit for Go since it's a backend only app, but far more complicated than a personal web site like this.

I love the safety net that a statically typed language gives you. Having worked in both, statically typed and dynamically typed languages, I can say that I prefer the former. Something that I also noticed is how much cleaner code is when the types of arguments to functions are explicit.

In my limited experience with Go I have not missed class hierarchies. I have been weary of class hierarchies in C# and Ruby for a long time, heck Gamma et al. stated that we should "favor object composition over class inheritance" [more than 20 years ago](https://en.wikipedia.org/wiki/Design_Patterns), but I was still a bit anxious on how I would feel once they are not available at all in the language. So far so good.

**Update May/2017** Blog posts from other developers describing their first experiences with Go: [Using Golang in Production - My Experiences](http://blog.tamizhvendan.in/blog/2017/05/01/using-golang-in-production-my-experiences/) and [Building a Go Web App in 2017](https://grisha.org/blog/2017/04/27/simplistic-go-web-app/)
