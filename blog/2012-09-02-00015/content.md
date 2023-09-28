<img width="320px" height="240px" align="left" src="https://hectorcorrea.com/images/mac_macair.jpg" alt="MacAir" />
A little more than a month ago I got myself a MacAir. Even though I’ve own computers since the mid 80s this is the first time that I buy a Machintosh. The reason I bough a Mac rather than a PC is because I wanted to **start experimenting with the web development tools that exist for non-Windows environments**. Although I’ve been a software developer for more than 15 years all my experience is with Microsoft Windows and I thought is about time I start experimenting with the tools that “the “other half of the world” is using.

Since I got a Mac most people would probably thing that I want to start writing applications for the iPhone or the Mac but in reality I want to experiment with the development tools for web applications. I will probably write an app or two for the iPhone or the Mac just to see what it’s like but my real motivation is web development using non-Windows tools.

This is not to say that I am abandoning development with Microsoft’s tools (or the Windows ecosystem) but rather I want to experience what else is out there. There are plenty of successful web sites (e.g. Google and Facebook) that are developed using non-Windows tools and I want to see what the development experience on those tools is like.

The programming language that I decided to learn first is *Ruby* along with the popular web development framework *Ruby on Rails*. Over the following months I will be posting my experiences developing applications on the Mac OS X using Ruby and Ruby on Rails from the perspective of somebody that is very familiar and comfortable with Microsoft/Windows development.


## Why Ruby?

<img width="240px" height="180px" align="left" src="https://hectorcorrea.com/images/ruby.jpg" alt="MacAir" />
When I decided to learn a new language I wasn’t too thrilled to learn Perl, PHP, or Phyton. I don’t really know why, the thought of learning any of those languages just didn’t excite me despite the fact that they are the ones used some of the most successful sites including Google and Facebook. 

But [Ruby](http://www.ruby-lang.org) gave me a different vibe, people seem to be very passionate and excited about Ruby and Ruby on Rails. Both of them seem to be quite popular to build web applications which is what I am most interested in. In addition a lot of the ideas for Microsoft’s ASP.NET MVC (which is my main development platform these days) were taken from Rails which made me think that there is definitively a lot to be gained from learning both Ruby and Rails.

In his presentation [10 Things Every Java Programmer Should Know About Ruby](http://onestepback.org/articles/10things/index.html) Jim Weirich has this great quote:

> “A language that doesn’t affect the way you think about programming is not worth knowing -- Alan Perlis”


## So What Does Ruby Offer? (or How I Learned to Stop Worrying and Love the Bomb)

The Ruby programming language is introduced like this in the [ruby-lang.org](http://ruby-lang.org) web site:

> “A dynamic, open source programming language with a focus on simplicity and productivity. 
> It has an elegant syntax that is natural to read and easy to write.”

In digging deeper I found that Ruby is object oriented, provides garbage collection, borrows some interesting features from functional languages (which is something I’ve been meaning to look into), and it has indeed a clean syntax.

However, there were also a few things in Ruby that I wasn’t too excited about. In particular the fact that Ruby is an interpreted language and that it is not statically typed (although it is strongly typed.) After writing code in C# for so many years I had a hard time imaging myself using a programming language that didn't offer these features.

With my background in C# I found the slides for [10 Things Every Java Programmer Should Know About Ruby](http://onestepback.org/articles/10things/index.html) by Jim Weirich and [Ruby for Java Programmers](http://www.softwaresummit.com/2006/speakers/BowlerRubyForJavaProgrammers.pdf) by Mike Bowler very interesting. Both of these presentations are packed with a lot information on what makes the Ruby language different from Java/C# and highlight its strengths. Some of the topics that they cover include:

* The Dynamic nature of Ruby makes factories and mock objects trivial
* Reflection is also taken to a new level (compared with what you can do in a statically typed language like Java or C#)
* Nil is an object which means you never get a null pointer
* Closures: an object that is a block of code

Jim’s slides also address my biggest concern (namely that Ruby is not statically typed) with a great quote from Bob Martin in which he mentions how the safety net provided by static typed languages is not as important when your code is covered with unit tests as those test would catch type errors for you. I am not sure I agree with this statement 100%. It is certainly an interesting point, though.

You can find these and other interesting articles at the [ruby-doc.org](http://ruby-doc.org/whyruby/) web site.


## Ruby on the Mac OS X

One of the nice things about OS X is that out of the box it comes with the runtime libraries for several programming languages including Perl, PHP, and Ruby.

This is really nice feature for me since that means I can start playing with Ruby without having too worry too much about where to get the binaries or how to compile the source code. In my case my Mac came with Ruby 1.8.7.

![Ruby Version](https://hectorcorrea.com/images/rubyversion.png)


## Beginning Ruby
<img align="left" src="https://hectorcorrea.com/images/beginningrubybook.jpg" alt="Beginning Ruby Book" />
I have found the book [Beginning Ruby: From Novice to Professional](http://www.amazon.com/Beginning-Ruby-Professional-Peter-Cooper/dp/1430223634/) by Peter Cooper a great resource to learn how to program in Ruby and get going.

Cooper does a great job of covering everything from how to make sure Ruby is installed on your machine, how to install Ruby, very basic programming topics (e.g. what is a class) to more advanced topics like exception handling, database access, web development or Ruby-specific techniques like how to implement enumerators and the yield operator, mix-ins, and code blocks, development frameworks for Ruby like Rails and Sinatra.

I highly recommend this book to anyone starting with Ruby.


## Development Tools for Ruby

The fact that the Ruby interpreter comes built in with the Mac OS X is great, but what about editors, debuggers, IDE, source control, and other development tools. For somebody like me used to a great IDE like Microsoft’s Visual Studio I was shocked to find that there is no “standard IDE” for Ruby on the Mac or any other operating system.

From what I’ve been able to gather, most Ruby developers are comfortable editing their code in a variety of text editors that provide color syntax, basic statement completion, and indentation but not much more. There does not seem to be a “visual debugger” like what Visual Studio provides for C#, there isn’t the concept of IntelliSense either. Despite this Ruby developers seem to be able to crank up code pretty fast.


### Editors

You can download for free the Mac OS X development tool (called [XCode](http://developer.apple.com/technologies/tools/xcode.html)) that is a full blown IDE and supports color syntax for Ruby but not visual debugger or much more integration.

I’ve found two text editors that seem to be both popular and powerful for Ruby development:

* [TextMate](http://macromates.com/) seems to be one of the favorite editors for Ruby (and Rails) development on the Mac as it provides really nice keyboard shortcuts for code completion, running unit tests, integration with source control (both SubVersion and Git) and other goodies. ~~This has been my favorite editor so far. It is not free but I will probably end up buying a license in the next few days.~~ **Update:** As I indicated in my [follow up post](https://hectorcorrea.com/blog/web-development-on-the-mac-os-x-part-ii/5) I ended up going for Sublime Text 2.
* [TextWrangler](http://www.barebones.com/products/textwrangler/) is a free text editor and supports color syntax for Ruby. Somehow I found the workflow for Ruby development not as good with this editor as with TextMate but you cannot beat the price.

Here is an screenshot of TextMate:

![TextMate](https://hectorcorrea.com/images/mac_textmate.jpg)

There are plenty of other editors that people use on the OS X to edit Ruby code that are common among *nix users including Vim and Emacs. Yet, for somebody like me, with a Windows background, those editors look and feel like something from the stone age. They can be very powerful once you learn them but the learning curve might be very steep.


### Visual Debugger

There is not a visual debugger like the one in Microsoft Visual Studio for Ruby on the OS X or other *nix environments. This has been one of the biggest shocks to me as I’ve gotten used to have one and still miss it.

One can make the argument that if your code is testable and properly structured (small methods, classes with single responsibility) the need for an advanced debugger is less critical. That might be true but I am still dumbfounded by the lack of one. I really hope that Ted Neward’s [prediction for 2011](http://blogs.tedneward.com/2011/01/01/Tech+Predictions+2011+Edition.aspx) that “Apple starts feeling the pressure to deliver a developer experience that isn’t mired in mid-90’s metaphor” comes true.

I should clarify that *there is a debugger in Ruby* that allows you to step through your code but it is nothing like what I was used to in Visual Studio.


### Source Control

Although more than two years old the article [7 Version Control Systems Reviewed](http://www.smashingmagazine.com/2008/09/18/the-top-7-open-source-version-control-systems/) in Smashing Magazine is a great resource to get an idea of what’s available and popular on the Mac OS X.

Of the source control systems mentioned in the article so far I have only experimented with Git and I am very pleased with it. As with the text editors I was a shocked with the lack of visual tools to do basic operations like check in and check out but this probably due in part to the lack of a standard IDE for Ruby development.

However, Git’s installation process on the OS X was incredible painless. I was pleasantly surprised on how well documented the process to [install it](http://help.github.com/mac-git-installation/) and get [started](http://www.kernel.org/pub/software/scm/git/docs/gittutorial.html) are. I did notice that Git seems very light-weight compared to a traditional source control system in Windows (e.g. Team Foundation or SourceSafe.) 

It has taken me a bit to get used to depend on command line tools to check in and check out my code but at the same time I am amazed how simple it becomes after a few days of using it.  I am even using GitHub to push my code “to the cloud” and keep a copy of it outside my machine. 


## In Summary

So after little bit more than a month of playing with Ruby on the OS X on my spare time, I am starting to get familiar with the language and the development tools. All of a sudden I find myself writing code without looking at my book (or the web), running my unit tests, making changes, and checking my changes to my local and remote source control repositories. I am not nearly as proficient with Ruby or the development tools on OS X as I am with C# or Visual Studio but I am starting to get to the point where I feel comfortable.

I am sure it will be a fun and interesting ride. I’ll keep blogging about my experiences of learning Ruby (and eventually Rails) and the development tools for OS X as I learn more.

** Update:** Check out [Web Development on the Mac OS X part II](https://hectorcorrea.com/blog/web-development-on-the-mac-os-x-part-ii/5) for an update on my experience 18 months later or [My (strange) journey to becoming a Ruby developer](https://hectorcorrea.com/blog/my-strange-journey-to-becoming-a-ruby-developer/82) for my experience after doing Ruby professionally for a few years.