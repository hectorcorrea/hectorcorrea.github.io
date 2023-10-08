# Practical Object-Oriented Design in Ruby
<img style="float:left;" src="https://hectorcorrea.com/images/poodr.jpg"/> Anyone that has done a decent amount of programming in Ruby is bound hear about the book [Practical Object-Oriented Design in Ruby](http://www.poodr.com/) <sup>[1]</sup> by Sandi Metz. When I started programming on Ruby I was no exception to this and kept hearing people talk about it. Having read a fair share of books on Object-Oriented Design over the years I wasn't too excited to read yet another book on the subject since eventually most of them start to look and feel alike. But I caved in, I picked up a copy of POODR (as the book is commonly abbreviated), read it and enjoyed it quite a bit.

This blog post is a short review on two areas that I found interesting about POODR. In particular I found the emphasis on message passing (rather than on class structure) that Metz puts on the book a very refreshing way to understanding object-oriented systems that I think is missing in many books on the subject. Additionally, true to its name, the book provides a practical approach to understanding key concepts of object-oriented design and how to apply them. The book does not shy away from some of the difficult situations that developers will encounter when designing object-oriented systems. Instead Metz explains these situations, provides guidance on how to avoid common pitfalls, and gives great advice on how to pick sensible strategies for most situations.

## Message Passing
As I mentioned in the previous paragraph, the first thing that struck me about POODR is that Metz took the unconventional approach of dedicating the majority of the book to the concept of message passing rather than to the structure of objects and classes when working on an object-oriented system. Although the focus on messaging is not a new idea (Alan Kay has been known to say this since at least the late nineties<sup>[2][3]</sup>), most books and articles on Object-Oriented Design (OOD) and Object-Oriented Programming (OOP) tend to put more emphasis on objects and classes than on message passing. For example, in [Wikipedia](http://en.wikipedia.org/wiki/Object-oriented_programming) the opening paragraph for Object-Oriented Programming goes like this:

> Object-oriented programming (OOP) is a programming paradigm based on the concept of "objects", which are data structures that contain data, in the form of fields, often known as attributes; and code, in the form of procedures, often known as methods.

The Wikipedia entry elaborates a little bit on how methods are used to modify the data of objects but the concept of message passing does not get more than a brief mention half way into the page under the "additional concepts" section. 

On the other hand in the introduction of her book Metz indicates that two thirds of it are dedicated to messages and only two chapters are about objects. Early on the book she indicates that object-oriented design is about managing dependencies caused by messaging between objects [p. 3] and that **although the most visible structure of an object-oriented system is the class, the foundation is the message**[p. 15]. 
 
Throughout the book Metz explains several core concepts of OOD and OOP through the lens of message passing. For example, when talking about inheritance she states that:

> Inheritance is, at its core, a mechanism for automatic message delegation. It defines a forwarding path for not-understood messages [p. 105]

and therefore inheritance can be seen as a "code arrangement technique": 

> For the cost of arranging objects you get message delegation for free [p. 184]

By focusing on message delegation Metz is able to describe some of the pitfalls that new developers run into when using inheritance incorrectly. For example, one of the biggest problems with deep class hierarchies is that it's hard to see and predict the effects of overwriting a method in a subclass because we are changing the method delegation chain with our implementation. Many of us have been bitten before when forgetting to call the parent implementation of a method in a subclass and having to track down unexpected behaviors because they are hidden in the class hierarchy. Again, in Metz's words:

> Deep hierarchies define a very long search path for message resolution. A class depends on anything above it, it's dangerous [p. 162]

Although some of these concepts have been discussed before in the OO literature <sup>[4]</sup> I appreciate the way Metz explains them using a layperson language that would allow beginners and intermediate developers to relate to them more easily.

On Chapter 6 Metz does a great job at explaining how shallow class hierarchies, hook methods, and the template method pattern can be used to get the best out of class hierarchies.

The book also does a great job of describing alternatives to inheritance when designing object-oriented systems including the use of Modules (or mixins) and the use of Composition. For example, I found particularly insightful the way Metz contrasts composition to inheritance in terms of message delegation:

> Composition is an alternative that reverses the cost and benefits. 
> In composition, the relationship between objects is not codified in the class hierarchy; 
> instead objects stand alone and as a result must explicitly know about and delegate messages to one another. 
> Composition allows objects to have structural independence, but at the cost of explicit message delegation. [p. 184]

Metz does a really good job of using plain language and realistic examples to describe the recommendations on when and how to use each of these techniques (e.g. inheritance for is-a relationships, composition for has-a relationships, and duck-types for behaves-like-a relationships [p. 188-190]).


## Design is the art of arranging code
Another thing that I like in POODR is how explicitly Metz binds software design to coding rather than considering design separate from the code:

> The code's arrangement is the design. [...] Design is thus an art, the art of arranging code. [p. 4]

I found the concept of binding design directly to coding very appealing. For many years I have disagreed when people say that a particular software system "was not designed". I believe that all software systems are designed, some systems are well designed and others are poorly designed, but all of them are designed nevertheless. Metz's approach makes it clear that all systems are designed since code is arranged in all systems. It is interesting that even Metz falls into the trap of mentioning "*undesigned* applications" [p. 7] when according to her own definition the proper wording would have been "poorly designed applications".

Throughout the book Metz talks about how systems evolve over time and that this constant change is what makes design so important. Over and over she emphasizes that the goal is to arrive to code that allows for easy changes:

> Software will change. The need for change is what makes design matter. [p. 3] 
> The purpose of design is to allow you to do design later and 
> its primary goal is to reduce the cost of change [p. 4] 
> Design is more the art of preserving changeability than it is the act of achieving perfection  [p. 16]

Most of the material in her book is dedicated to teach developers what are some of the concepts and practices that they could use to allow their system to evolve with time. The book covers concepts like single responsibility principle, dependency injection, duck-typing, inheritance, composition, modules (mixins), unit testing, and interfaces in a very pragmatic way. 

I appreciate how honest Metz is throughout the book in showing some of the common problems with naive code implementations that tend to be hard to change over time while also acknowledging that sometimes better design leads to code that is a bit harder to read given that is a bit more abstract and the functionality is spread out over many classes. She is not shy to state that:

> Design is not a fixed set of rules, design is about tradeoffs and decisions [p. 4]

For example, after refactoring a piece of code in chapter 8 to use composition she acknowledges that in the new and better implementation:

> while each individual abstraction might be easy to understand, 
> there is no single place in the code that makes obvious the behavior of the whole [p. 187]

Metz is also very realistic on what good design can and should achieve. For example, she encourages developers to try to come up with solutions that are both cost effective and that leave our options open for changes in the future "our code does not need to guess the future, it preserves your options" [p. 4] and also provides guidance on how to decide *when* to make a change, for example she suggest that "when the cost of doing nothing is the same as the current cost, postpone the decision" [p. 22].

Early on the book Metz acknowledges that "applications are never perfectly designed" [p. 16] and at the end of it she reiterates this message when she advises people to be mindful of the rules that she presented but also encourages developers to practice and learn when to break these rules:

> Now that you know these rules you can bend them to your own purposes. 
> The tension inherent in design means that these rules are meant to be broken; 
> learning to break then well is a designer's greatest strength [p. 241]


## Criticism
I really don't have a lot to criticize about this book, but here are some of the areas that I am not sure I fully agree or like about it.

By page count, POODR is a small book for books on its category<sup>[5]</sup> and yet it manages to be full of great advise and insight on how to design object-oriented systems. However, I was a bit disappointed that there is no mention of database access or development using the popular Ruby on Rails framework *given how much of Ruby development happens on systems that use both*. I suspect these two topics were not included to keep the book short and focused (which is a laudable goal) but it is something that I would have liked to see on a book as pragmatic as this.

The book is targeted to Ruby developers and it does a great job at explaining object-oriented concepts using Ruby. However, I couldn't help but cringe at some of the acrobatics that Metz goes to explain how to do proper testing in Ruby when using mocks and stubs. Since Ruby (unlike Java or C#) does not provide formal *interfaces* it is easy for tests using stubs to fail to detect changes in the actual class that they represent and therefore tests can become stale. This is not a problem with Metz's approach to testing per-se but something that Ruby developers must be aware of. Metz alludes to this problem early on the book when she says "in static languages designing an interface is always intentional" [p. 54] but I think she should have reiterated this when the tests on chapter 9 start to get convoluted because of missing features in the language.


## In Summary
I found POODR to be a great book for anyone interested in the design and development of object-oriented systems. Like everybody else, [I consider myself above average](http://en.wikipedia.org/wiki/Illusory_superiority) when it comes to knowledge of object-oriented design and yet I got a lot out of this book.

As I mentioned earlier on this blog post, by focusing on message passing Metz is able to describe what makes object-oriented designs work (and fail) and what are the best techniques for every situation. She manages to convey a great deal of information and tackle difficult problems while using easy to read terminology and plain language.

I would recommend this book hands down to anyone doing Ruby development. Although the book takes advantage of a few specific features of the Ruby language (e.g. duck-types and mixins) when explaining some of the concepts, people using statically typed languages (like Java or C#) would also benefit from reading this book given that the key OO concepts and practices are explained in great detail and they apply to any kind of object-oriented system regardless of the language.

In summary, if you are doing software development, you should read it.

**[Update March/2015]** Nathan Youngman has a great blog post titled [Go Object Oriented Design](http://nathany.com/good/) in which he converts from Ruby to Go one of the examples presented in POODR and shows how the concepts apply to Go.


## References
[1] All page references in this blog post are for the first edition of Sandi Metz' [Practical Object-Oriented Design in Ruby](http://www.amazon.com/Practical-Object-Oriented-Design-Ruby-Addison-Wesley/dp/0321721330) 

[2] See [Alan Kay on messaging](http://c2.com/cgi/wiki?AlanKayOnMessaging) ("The big idea is 'messaging'")

[3] See [this e-mail exchange](http://www.purl.org/stefan_ram/pub/doc_kay_oop_en) in which Alan Kay discusses the two camps on object-oriented programming.

[4] On their book [Design Patterns: Elements of Reusable Object-Oriented Software](http://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612/) the Gang of Four are famous for describing some of the intrinsic problems with inheritance. For example on page 19 they say "because inheritance exposes a subclass to details of its parent's implementation, it's often said that inheritance breaks encapsulation" and on page 20 they state that we should "favor object composition over class inheritance"

[5] POODR is 272 pages long. Compared to Grady Booch's *Object-Oriented Analysis and Design with Applications* (600+ pages) and Bertrand Meyer's *Object-Oriented Software Construction* (1200+ pages) this book can be called small.

![Bike gears](https://hectorcorrea.com/images/bikegears.jpg)