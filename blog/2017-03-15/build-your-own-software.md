# Build your own software
This blog post is an extended version of the notes that I used for my talk titled [Build your own software, yes, build it](http://2017.code4lib.org/talks/Build-your-own-software-yes-build-it) at Code4Lib in Los Angeles, California on March 7th, 2017. You can also watch the [recording of the presentation](https://youtu.be/eUArNAG-CY4?t=2h57m30s), it's about 20 minutes long.

## Introduction

This post, like the original talk at Code4Lib, is an attempt to create a discussion about the benefits of writing our own software solutions as opposed to adopting large open source solutions. During my (short) tenure within the Libraries I’ve gotten the impression that people almost always prefer to adopt large open source solutions over developing one. By “large open source solutions” I mean projects like [VIVO](http://www.vivoweb.org/), [Hydra](https://github.com/projecthydra), [Islandora](http://islandora.ca/), and [FOLIO](https://www.folio.org), to name a few.

While I think open source is a wonderful idea and brings great opportunities for collaboration, I believe in the libraries we have swung the pendulum too far towards adopting large open source solutions rather than building our own software. Adopting large solutions seems to be the default mode these days. This post tries to counter this approach by purposely highlighting the disadvantages of adopting open source solutions as well as emphasizing the advantages of building our own software.

In my experience people tend to oversimplify what it takes to adopt an existing open source solution developed by (or for) another institution as well as underestimate the long term maintenance ramifications of this approach. I am also concerned that we have stopped thinking and talking about the needs of our users and what we could and should build for them. Instead we focus on what open source solutions we can adopt. We box ourselves prematurely. We have shifted the conversation from user needs to available open source products sidestepping our users’ needs.

This post is not an attempt to bash open source. I love open source. I have a lot of respect for the people that build those products and the communities that surround them. I personally participated for almost two years in the development of Sufia, a well-known Hydra head. I want us to continue sharing the design ideas as well as the source code of the work that we do. Throughout this post I will use examples from a variety of existing large open source projects to help clarify what I mean and make the ideas concrete. But keep in mind that the ideas presented here apply to any large open source solution, not just the ones mentioned in this post.

I will also discuss areas where I believe adopting an open source solution is a good fit. This post is not to suggest that you build everything from scratch or that you should “go alone”. As I mentioned before, I think open source adds value to our work, but we should be judicious when and how we use it.

I don’t think that there is a one-size-fits-all approach to software development. For example, the needs and capabilities of a one-developer-team are different from those libraries that have a team of 4-5 developers. Some of the ideas that I will present might apply to your team, others might not. YMMV.


## Premise: Software is complex

Why am I advocating that we write our own software? Mostly, because software is complex. I realize this sounds a bit counter-intuitive, I am saying that software is complex and then I am saying that we should do more of it, but I hope to clarify throughout this post why I think this makes sense.

First, what do I mean by complexity? I am going to use Donald Norman’s definition. In *Living with Complexity* he says:

> "Complexity suggests things with many intricate and interrelated parts,
> which is different from complicated which suggest things that are confusing”

Norman says that “complexity describes the state of the world, complicated describes the state of the mind”, in other words, some things are complex.

In this post I will talk about the complexity of software in terms of technology but also as it relates to the social component of software development. Recognizing the social component of software development is not something new. In 2009 in *Software as History Embodied* Nathan Ensmenger said:

> "If we consider software not as an end-product or a finished good,
> but as a heterogeneous system,
> with both technological and social components,
> we can understand why the software maintenance problem
> has historically been so complex."

This idea has been discussed too in the context of software for libraries. In 2015, at Code4Lib in Portland, during her talk *Your code does not exist in a vacuum* Becky Yoose said:

> "Technology is more than just lines of code.
> It is part of our lives and livelihoods in the case of library technology […]
> it interacts with everything around us."

Given that software development is about technology and people, we have to address complexity on two fronts: technical and social.

Another insight that Donald Norman provides is that, although you cannot remove complexity from a large system, you can manage it. I believe that by writing our own software we can better manage complexity in two ways.

First, we can reduce the complexity of the solutions that we build. A solution built for one purpose in one institution can be less complex than one built to handle the needs of many institutions. Notice that I don’t say that we develop simple solutions because I am acknowledging that software is complex.

Second, throughout this post I will advocate that we should treat software development as a learning process and not as a product. By writing our own software we get to learn about the software and the institution in a way that is more appropriate for long term sustainability (compared to adopting something written by someone else for somebody else.)


## Software as a learning process

In a 1985 essay titled *Programming as Theory Building* Peter Naur said that we should view programming as an activity by which the programmers “achieve certain kind of insight” and not just as the “production of a program and other texts”. He goes on to indicate that the knowledge that programmers gain by writing the program “transcends that which is recorded in the documented products”. In 2011 Barn and Clark revisited Naur’s paper and found that it applies to the development of Enterprise Architecture models as well.

During the development of any software the developers and the domain experts (in our case librarians) arrive to a shared understanding of the problem to be solved and what the software can realistically do. The resulting software has this shared understanding codified as source code, and it makes sense to the people involved during the development because they learned it and worked it out together. Software solutions and the team that produce them become a part of the institutional knowledge.

Educational researchers like Ambrose and colleagues say that "Learning is a process, not a product". I suggest that we should treat software development in a similar way, as a process, not as a product.

When we adopt an open source solution we skip the learning process that happened during its development. We tend to focus on how great it is that we got source code and development hours for free, but fail to recognize that source code comes with a set of assumptions and knowledge that worked for the team that built it. But we don’t get the team (developers and librarians) that built the software nor the learning that they gained during the development of it. The larger the open source solution that we adopt the larger the knowledge gap becomes.

Mike Davidson has an interesting blog post in which he says that IT organizations should focus on “what did you learn last quarter” rather than “what did you ship last quarter”. I think this applies to software development at libraries too. Let us not sacrifice the learning process that comes with building our own software, we should value the learning part as much as, if not more than, the resulting software.


## Complexity in technology

What about the technology part? How does building your own software helps with this?

One of the things that I’ve seen people do is adopt open source solutions in programming languages that they don’t command. For example, I’ve seen Python shops adopting Hydra with a minimal command of the Ruby language. The same goes for teams adopting VIVO without any expertise in Java. This can be a huge setback for your team. Asking your team to adopt a large codebase that they did not write is a big undertaking. Adding new programming language on top of it makes it an even bigger challenge.

Although many in the social sciences have concluded that reductionism (i.e. if I understand the parts I can understand the whole) is an approach that does not work, in the software development community many still seem to think that it does. Some teams believe if that if they review the parts of the system (e.g. diagrams, code, classes, database structures, and tests) they can make sense of the whole system. I believe this misconception arises when we see software development as a product and not as a learning process as suggested in the previous section.

Another technical complexity that arises when you adopt a large open source solution comes in the form of customizations. Institutions tend to favor open source solutions, as opposed to proprietary ones, because they can customize them. However, if you customize an open source solution significantly you risk ending up in a fork of the source code that is hard to merge back or upgrade with the original. This is something that happens a lot and yet we tend to ignore this and promise ourselves that “next time we’ll be more careful”. This is a real problem, make sure you consider it.

One of the reasons I think building your own software is a good alternative is because you are in total control of the upgrades. You decide how much new functionality or new technologies you put out on every new release. Incremental updates on a code base that you know and understand are easier than on one that you don’t. I think building your own software is a way to manage this complexity and mitigate risk.

Dan McKinley wrote an interesting post in which he says that teams should limit the amount of innovation that a team introduces at once. When you build your own software you get to make that decision, you can decide to rewrite a new component in a new language or try a new database for particular feature of the system. Test it out in production, see how you feel about it and iterate (or perhaps rollback.) When you adopt an existing open source solution you get everything at once, there is no easy way to iterate and experiment, and that is a big risk.

Technology is a rapid changing field, a lot of new technologies come and go every year. Some teams like to experiment with new technologies as they come while others are more conservative on their approach. Follow what works for your team, there is neither a penalty for using older and well-established programming languages nor brownie points for using the latest tools. At the end of the day your users don’t care about the technology you use as long as you meet their needs.

Speaking of user needs. Building your own software is a great way to keep focus on what you users really need. Sometimes you’ll find out that what you really need is a web site that just looks better (go ahead and hire a designer) while other times you’ll realize a large architectural change is needed to support new requirements. Whatever the case might be, building your own software allows you to make that decision on a case by case basis. Agile calls for being iterative and incremental, not for implementing all the features at once, building your own allows you to make these increments at your own pace.

As a community, we should shift the conversation away from the technical solutions available and more into our requirements. We should talk about discovery and the needs of our patrons, not about Solr or Blacklight or VuFind. We should talk about the needs of our researchers to showcase their work and connect with each other, not about VIVO. We should talk about the needs of our institutions to preserve digital assets, not about Hydra or Islandora.

I think is important to realize that if you see a team succeed delivering a great piece of functionality to their users, it is likely because they worked well together, not because the tool that they used.


## When is open source a good idea

As I indicated earlier in this post, I love open source and see a great value in adopting open source solutions in areas of our work. In this section, I’ll highlight the areas where I think open source solutions are a great fit for our systems.

I think we should continue adopting (and producing) small open source utilities that are limited to a single tier (either the user interface, the middle tier, or the data tier.) These single-tier utilities are much easier to adopt and modify than the ones that cross many layers.

There are many open source programs popular among libraries that follow this single-tier approach. I’ll mention a couple here. One is Traject, a Ruby gem that allows you to parse MARC files and dump the data into Solr regardless of what front-end you use to display your data to your users. Another example is StackView, a jQuery plug-in, that allows you to display items from your catalog emulating a “virtual stack” regardless of your backend, all you need to give it is a JSON feed with the items to display or an API endpoint where it can fetch the data.  These tools do one thing well and have few external dependencies.

Another consideration when adoption open source solutions is whether they are read-only or read-write. It is significantly simpler to adopt an open source solution that only performs read operations than one that does read-write operations. This is perhaps one of the reasons Blacklight has seen such a great adoption. Although Blacklight crosses many tiers (from Solr all the way to the UI via Ruby code) it only performs read operations on your Solr data. Keep this in mind as you decide to adopt an open source solution. The complexity of adopting a read-write open source solution like Hydra, VIVO, or Folio, grows rather fast compared to a read-only solution.

Perhaps one of the biggest advantages of large open source solutions over building your own is the community aspect of it. Large open source solutions tend to have a large community of people around them that provide an environment of comradery and support that is hard to match if you build your own.

Another positive aspect of large open source solutions is how fast they are to address security vulnerabilities on the software that they use. These communities tend to release updates with the required security patches relatively quickly after a known exploit has been announced. Your team still needs to apply the patch to your local version, but, as a community, they then to be very responsive to keep the master branch up to date on security patches. If you build your own you need to make sure your team keeps an eye on it and usually not all teams are very good at this.


## In closing

Software has become increasingly important to libraries, and, although I wouldn’t go as far as saying that “Libraries are Software” as Cody Hanson blogged a few years ago (which by the way is an excellent post) I think that, because software is crucial to so much of what we do, we should become more involved in the development of it, and we should fully understand software that drives critical parts of our institutions.

Software development is a young discipline, we don't have hundreds of years of theory and practice. In her 2015 presentation Yoose mentioned the concept of “Technological Somnambulism” that suggests that we are “sleepwalking in our mediations with technology”. I would add that we are also trying to understand the means by which we develop our software and its implications.

A few months ago I was reading a book that described how anthropology evolved from the 1800s to today that I found surprisingly similar to our approach to software development. Crawford and Newcomb describe how the first anthropology books were written by people that interviewed others that had visited faraway lands, but the writers themselves never visited such places, they just wrote the stories relayed to them. As time went on, anthropologists realized they had an incomplete understanding of any given culture and that they themselves should travel to those places before writing the accounts. That was an improvement over the previous approach, but anthropologists soon realized that they lacked context to interpret what they were experiencing in the observed culture. It was not until anthropologists stayed for long periods (months or years) that they could better understand and explain a new culture and its practices. As Crawford and Newcomb indicate, this is what anthropologists call today “doing fieldwork”.

I believe we should use a similar approach in software development. By building our own software we get to “do the fieldwork” and gain a complete understanding of the institution, the social parts of the problem that we are trying to solve, as well as the technology that is used. Without this understanding our ability to deliver software solutions is hampered.

As I said throughout the post, I think building our software allows us to also manage the complexity of software in two fronts. First, we get to build solutions that match only the requirements that matter to our users and those can be less complex than solutions that do many things for many institutions. Second, by building our own software we get to learn about the institution and the technology in a way that is more suitable for long term maintenance of the resulting software.

I believe we should continue to share our ideas, our designs, and our source code, but I also believe that we should keep small the size and scope of these open source solutions. We should not try to share and/or adopt large open source solutions without taking into consideration the learning process that we lose when we decide not to build a particular piece of software. Remember to treat software development as a learning process, not as a product.


### References
* Ambrose S.A., Bridges M.W., DiPietro M., Lovett M.C., & Norman, M.K. (2010). *How Learning Works: Seven Research-Based Principles for Smart Teaching*. John Wiley & Sons.
* Barn B.S., Clark T. (2011) *Revisiting Naur’s Programming as Theory Building for Enterprise Architecture Modelling*. In: Mouratidis H., Rolland C. (eds) Advanced Information Systems Engineering. CAiSE 2011. Lecture Notes in Computer Science, vol 6741. Springer, Berlin, Heidelberg
* Crawford, D., & Newcomb, R. (2013). *Encountering Morocco: fieldwork and cultural understanding*. Bloomington, IN: Indiana University Press.
* Ensmenger, N. (2009). *Software as History Embodied*. IEEE Annals of the History of Computing 31(1), 86-88. IEEE Computer Society. Retrieved March 14, 2017, from Project MUSE database. Retrieved from: [https://muse.jhu.edu/article/263929/](https://muse.jhu.edu/article/263929/)
* Hanson, C. (2015, September). *Opinion (Libraries are Software)* [Blog] Retrieved from: [http://codyhanson.com/writing/software.html](http://codyhanson.com/writing/software.html) 
* McKinley, D. (2015, March 30). *Choose Boring Technology* [Blog] Retrieved from [http://mcfunley.com/choose-boring-technology](http://mcfunley.com/choose-boring-technology) 
* Naur, P. (1985). *Programming as Theory Building*. ScienceDirect, 15(5), 253-261. Retrieved from [http://pages.cs.wisc.edu/~remzi/Naur.pdf](http://pages.cs.wisc.edu/~remzi/Naur.pdf) 
* Norman, D. A. (2011). *Living with complexity*. Cambridge, Mass.: MIT Press.
* Yoose, B. (2015). *Your code does not exist in a vacuum*. [Conference slides and notes]. Retrieved from: [https://code4lib.org/conference/2015/yoose](https://code4lib.org/conference/2015/yoose)
