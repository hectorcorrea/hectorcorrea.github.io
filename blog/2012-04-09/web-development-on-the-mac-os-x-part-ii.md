# Web Development on the Mac OS X (part II)
Last year I blogged about [my first impressions using a Mac for web development](https://hectorcorrea.com/blog/ruby-development-on-the-mac-os-x/15) from the point of view of somebody with a strong Windows/C# background. This blog post is a long overdue continuation to that post.

I bought a Mac Air a year and a half ago with the intention of experiencing what is like to develop web applications in a non-Windows environment and it has been a great learning experience. I am amazed how much I've learned in those 18 months and the breadth of technologies and tools that I've played with in that time.


## Hardware and built-in software

The Mac Air is undoubtedly a fantastic piece of hardware, it's slim, lightweight, powerful, and overall a beautiful piece of hardware. It's not cheap, but it's worth every penny.

Learning OS X for somebody that has always lived in a Microsoft Windows world was not hard. I really thought I was going to struggle more with OS X but I didn't. The transition to OS X was pretty easy for me. Having said that, I don't find OS X incredibly better than Windows 7. There are a few differences here and there but nothing earth shattering.

Some of the software that comes bundled with OS X is certainly better than its Windows counterpart. For example iPhoto is a much better photo management tool than the standard photo software that has always come with Windows. iTunes (not surprisingly) seems to work better on the Mac than on Windows. The operating system updates also <i>seem</i> to work smoother than Windows updates and be less frequent and pesky. Support for viewing and creating PDFs is native (it even supports annotating PDFs.) Other tools like Finder are no better (or worse) than Windows Explorer to browse the file system. I did find surprising the lack a a built-in Paintbrush application, though.


## Software Development

As far as software development for web applications is concerned I have enjoyed learning what's available on the Mac much more than I had anticipated. Since I got my Mac I've gotten my hands on Ruby, Sinatra, Ruby on Rails, CoffeeScript, Node.js, Apache, MySQL, Heroku, git, github, xCode, TextMate, Sublime, and a bunch of other tools that I had never used before.

<img src="https://hectorcorrea.com/images/ruby_logo.jpg" style="float:left;" alt="Ruby Logo "/>As I blogged a year ago, the language that I decided to learn first was Ruby and the popular web development framework Ruby on Rails. That was certainly a great and fun experience.

I found the **Ruby language easy to learn** (although I don't call myself an expert on it by any means) and there are a lot good books and web sites with information on how to get started.

<img src="https://hectorcorrea.com/images/rails.png" style="float:left;" alt="Ruby on Rails logo" />I also enjoyed learning **Ruby on Rails** and gained appreciation for an "MVC framework" that truly provides support for models (the M in MVC.) In my web development experience with ASP.NET MVC I knew that there were other frameworks that did a more complete job but I didn't quite get it until I understood how things like ActiveRecord and database migrations work in Rails. 

Rails was not as easy to understand as I had heard before. Rails is actually quite complex to get started since it does so much and there are so many pieces that work together. In addition, people seem to use many different options and configurations when working on Rails that baffled me for a while. For example, it is possible (and common) for people to use different web servers to host Rails applications and having to learn the pros and cons for Apache, Passenger, Mongrel, WEBrick, and when they are a good fit was a bit overwhelming for somebody new to the OS X environment. This is in contrast to ASP.NET MVC web development in which the web server is almost always IIS. The same goes for view rendering engines, in Rails people use Embedded Ruby (erb), HAML, Mustache, and a bunch of other tools that can make the process of learning Rails confusing once you get pass the "hello world" web page. I don't mean to indicate that these options are a knock against Rails, but they can make it hard for the beginner to understand what's the best way for him/her to take.

<img src="https://hectorcorrea.com/images/sinatralogo.png" style="float:left;" alt="Sinatra logo" />I also enjoyed a lot learning **Sinatra**. [Sinatra](http://www.sinatrarb.com/) is more similar to ASP.NET MVC than Rails because it only provides support for views and controllers (the V and C in MVC.) I found Sinatra's learning curve much sorter than Rails but be aware that you get much less out of the box with Sinatra than you do with Rails. The [binary tree drawer](https://hectorcorrea.com/blog/drawing-a-binary-tree-in-ruby/13) that I put together in a previous blog post uses Sinatra. 

I would recommend learning Sinatra before learning Rails because the learning curve is much shorter and less complicated. However, if you are planning on doing a traditional web applications with a relational database Rails is definitively a great fit. All the "extra stuff" that Rails provides are things that you will need once your application grows and it's deployed to production. For those kinds of applications (which are very common) Rails is definitively a more mature and complete product.

During the process of learning Ruby and Rails I started hearing about CoffeeScript and got interested on it.

<img src="https://hectorcorrea.com/images/coffeescriptlogo.png" style="float:left" alt="CoffeeScript logo" /> **CoffeeScript** is a language that compiles to JavaScript and provides a very pleasant way of writing good JavaScript applications. I rewrote in CoffeeScript the program that I had written in Ruby a few months prior to draw binary trees and I was amazed how closely the CoffeeScript code is to the Ruby code. You can learn more about my experience, the source code, and see a demo version [here](https://hectorcorrea.com/blog/drawing-a-binary-tree-in-coffeescript/3).

<img src="https://hectorcorrea.com/images/nodejs.png" style="float:left;" alt="NodeJS logo" />Learning CoffeeScript itself lead me to read and become more aware of **Node.js** and decided to take the plunge and start learning that too. There is a lot of stuff going on with Node.js in the community and new libraries and utilities are coming out at a fast pace.

I am not sure if CoffeeScript and Node.js are going to become the next Ruby on Rails as [some people are suggesting](http://mashable.com/2011/03/10/node-js), but I do agree with Giles Bowkett when he [says](http://gilesbowkett.blogspot.com/2012/02/rails-went-off-rails-why-im-rebuilding.html) "you should learn Node.js, CoffeeScript, and Backbone because the work going on in those communities changes paradigms."

In my blog post last year I mentioned that the lack of integrated development environments (IDE) for Ruby dumbfounded me. As I asked around it turns out that there are several good IDEs but most people are comfortable using text editors with some advanced features.

The same is true for CoffeeScript and Node.js. There is no "standard IDE" that everybody uses. Quite the opposite, people use a variety of text editors with some development features like auto-completion, color syntax, and ways to kick of the application but nothing remotely compared to the features provided by Visual Studio. This seems to be the norm in the Mac (and Linux) world. This diversity of tools is very different from the Windows world where pretty much all the web development happens in .NET languages and using Visual Studio.

For my part I ended settling on [Sublime Text](http://www.sublimetext.com/2) as my editor of choice and bought a license for it.


## Conclusions

As I said at the beginning of this post, I am really happy with the large amount of technologies and tools for web development that I've got to experiment and learn on the Mac in the last 18 months.  Although I am pretty sure that I could have learned and played with most of these technologies on a Windows machine, I am glad I decided to do it on a non-Windows machine to be able to compare and contrast first hand.

Macs are very popular among web developers that are not using the Microsoft stack and I enjoyed being able to experience how things are done in this environment.

In the end I think that both Macs and Windows environments are very powerful and fun to work with, each of them with their own strengths and weaknesses.

A few days ago I noticed that I had not turned on my Windows machine at home in over 4 months, that's probably a telling sign of how much I am enjoying the Mac as a whole.

**Update**:  A few years later I ended up taking my first  job as a Ruby developer and blogged about my experience 5 years into it in [My (strange) journey to becoming a Ruby developer](https://hectorcorrea.com/blog/my-strange-journey-to-becoming-a-ruby-developer/82).