# Code4Lib 2015
<img src="https://hectorcorrea.com/images/pdx_mthood.jpg" style="float:right;" alt="Mount Hood" /> [Code4Lib](http://code4lib.org) is an annual conference that brings together a mix of people that work at libraries and/or develop software for libraries. Given that I am a newcomer to the library domain, this was my first time attending and I learned a lot. 

Among the things that I found interesting is that Code4Lib is a single-track conference. This means that all 400+ attendees are on the same room listening to same sessions all the time (except the pre-conference workshops.) I have not attended a single-track conference in a long time but I liked several things about this approach.

* It forces the organizers to be very selective on the sessions that will be presented. Will this session be interesting for 400+ people?
* It also forces the presenters to focus on things that will appeal to a broader audience. Will 400+ people be interested in what I have to say?
* It keeps everybody on the same page throughout the conference. If you bump into somebody you know for sure they attended the same sessions as you did and it&rsquo;s easier to start a conversation.
* Because everybody will be on the same room you don't get *us versus them* kind of sessions. Having everybody in the room at all times forces a more honest and balanced approach to certain topics like the relation between managers and developers or between IT and librarians.
* Likewise, the single-track approach makes sure everybody hears some of the difficult social issues presented at the conference. You do not get to chose whether you want to hear about the struggles of under represented groups or not, we all hear the same sessions. 

Most of the conferences that I have attended in the last 10 years have been technology oriented only. Code4lib on the other hand has [a mix of topics](http://code4lib.org/conference/2015/schedule) including technologies for libraries, team dynamics, social issues, and so on. This is the first conference that I attend where I learn about maternity leave in the morning and about Solr indexing in the afternoon.

The conference had a mix of session styles including keynotes (45 minutes long), "normal" sessions (20 minutes), and lightning talks (5 minutes) that makes for a nice pace throughout the day. I didn't take notes on all the sessions but I'll try to summarize some of my favorites.


## Pre-conference Workshops
<img src="https://hectorcorrea.com/images/pdx_bikers.jpg" style="float:right;" alt="Bikers" /> Monday morning I attended the [Linked Data Workshop](http://goo.gl/QrUIYE) by Karen Estlund and Tamsin Woo. This was a very timely topic for me given that I am new to the libraries and they covered some of the issues that Linked Data (LD) and the Resource Description Framework (RDF) are trying to solve and how they map to the library domain. 

Karen and Tamsin talked about the difference between Linked Data and RDF ("RDF is a mechanism to leverage Linked Data") and the importance of using URIs for predicates and objects to enable linked data to work across institutions. I appreciated the broad overview and real life examples that they provided on how libraries are using these tools. One of the key points of this session was that "if your data isn't reusable, shareable, and machine readable then you're not doing enough".

I also liked the exercise in which we had to map a list of terms to linked data terms from [Linked Open Vocabularies](http://lov.okfn.org/dataset/lov/). I was lucky to be paired with two others (Sandy from UMKC and Richard from Berkeley) that knew how libraries work and provided me with some background on the needs of libraries and the technologies that they use outside of Linked Data and RDF.

In the afternoon I attended the [DPLA API Workshop](http://bit.ly/c4l15-dpla-api) in which the DPLA team gave an introduction on how to use their API to fetch information from their repository. They managed to get 20+ people to install a plug-in to issue HTTP GET/POST request via the browser, get a DPLA API-Key in realtime, and start issuing HTTP requests to fetch data in a very short amount of time and to an audience with a rather broad level of expertise using HTTP APIs.

I posted a small code sample to demo how easy is to get data from DPLA using plain HTML and JavaScript. You can see the code in [this gist](https://gist.github.com/hectorcorrea/c1419e5ea8791cab14bf) on GitHub or, if you have a DPLA API key, a running version of it [here](https://hectorcorrea.com/demos/dpla.html).


## Conference Sessions
<img src="https://hectorcorrea.com/images/pdx_portlandia.jpg" style="float:right;" alt="Portlandia" /> [Selena Deckelmann](https://speakerdeck.com/selenamarie/code4lib-what-beginners-teach-us) kicked off the conference with a great keynote on how to be welcoming to people starting in software development. In particular I liked how she asked people to put themselves on the shoes of the beginners so-to-speak when trying to help them:

> Next time you answer a beginner's question take a moment to consider... 
> How will the beginner feel when they hear your answer

I also liked how she spoke about the elephant in the room when it comes to the complexity in getting a development environment ready, particularly for newcomers. I think she called this the "unwritten first chapter" and the need for us to help people over this big hurdle when they first start writing software.

[Becky Yoose](http://code4lib.org/conference/2015/yoose) gave a great presentation on how the development and use of technology tools has deep implications in the people that produce them and the final consumers:

> Technology is more than just lines of code. It is part of our lives and livelihoods 
> in the case of library technology, or libtech, and it interacts with everything around us.

She talked about some of the benefits and complications for librarians to work in an open-source software community from the technology point of view as well as the implications for the developers and the final consumers. The fact that the software development world (including open-source software) hasn't been friendly to women and that the majority of librarians are women makes this topic a great fit for the conference.

[Jason A. Clark and Scott W. H. Young](http://code4lib.org/conference/2015/clark) presented the work that they have done to make books available via the browser without the need of special devices or software to read them. They talked about the concept of "Library as a Publisher". I found this presentation fascinating for two reasons. One is the geek aspect of making beautiful and meaningful displays of data (text and images) in the browser without plug-ins, and the second reason, is the implications of taking the content of the book to a semantic level where it can be linked and discovered through standard tools and technologies for the web.

[Kevin Clark](http://www.kevinclarke.info/slides/c4l15/index.html) talked about Packer.io, a tool to create "identical machine images for multiple platforms from a single source configuration". He showed scripts used to deploy a machine to AWS or DigitalOcean among others.

Although I love tools like Packer.io for the *deployment* of products to production I am a bit skeptical about using them for development environments. Not because the tools are not good enough, but because it showcases how complicated we have made the setup process (something that Selena Deckelmann addressed on the keynote) and instead of acknowledging the problem and trying to fix it we are sidestepping it, potentially making worse with yet another abstraction layer on top.

[Wayne Schneider](https://github.com/wafschneider/dynamic-indexing-code4lib2015/blob/master/Dynamic%20indexing%20code4lib%202015.pdf?raw=true) gave a great presentation on how they dynamically index 1.5 million MARC records into Solr and how they deal with failures and updates. Really cool stuff.

One of my favorite sessions was [Jason Thomale's](https://rawgit.com/jthomale/c4l2015-presentation/master/slides.html#slide-100) presentation on how difficult is to come up with a meaningful search interface. He talked about how everybody just says "make it simple like the Google search box" but that the real problem is behind the search box. In order for search results to be meaningful the data has to be linked, machine readable, and weighted. He described how they analyzed whether people have different search patters for different kind of media (e.g. articles vs books) and how that would affect the defaults that they provide in their search tools. 

[Andromeda Yelton](http://andromedayelton.com/blog/2015/02/16/c4l15-keynote-transcript/) gave a beautiful and thoughtful talk titled "Architect for wanderlust: the web and open things" in which she talked and weaved together a variety of topics including the history of the web, how Code4Lib started 9 years ago, the evolution of libraries and library technologies, and the place of libraries in society. Her closing remarks sum up the vibe that she generated during this talk:

> I want us to spend the next nine years inventing, building, library software. Building systems that question our own assumptions. That intentionally remove barriers and make space for all kinds of people, from all kinds of backgrounds, to tell their own stories, to build their own technology, to use in their own ways, that transform themselves in ways that matter to them. I want us to decenter ourselves so the systems we build aren’t things we own but things we give, and can then evolve in ways that we can’t predict. I want us to build library software. Architect for wanderlust.


## Lightning talks
Each day there was an hour or so reserved for lightning talks in which people volunteered to present a wide variety of topics (you can find the entire list [here](http://wiki.code4lib.org/2015_Lightning_Talks).)

Shameless plug: I gave a lightning talk titled [RDF for relational heads](https://gist.github.com/hectorcorrea/c49dc287581d2856a12e) in which I talked about how to understand RDF if you come from a relational database background and how the tools available in the Hydra stack hide some of its complexities.

[Trey Pendragon](http://www.slideshare.net/JosephSchmoseph1/become-better-developers-44564801) gave a great talk on how to improve as software developer. I particularly liked how he emphasized that the best way to improve is to work with at least another developer because there is only so much one can professionally grow in a solo environment.


## Closing thoughts
As I said at the beginning of this blog, Code4Lib was quite a new experience to me. Not only I got exposure to technologies that I need to learn and understand better, but also how they fit in the grand scheme of things. In addition, I got a much better understanding of the goals that libraries are setting for themselves, some of the challenges that they face, and how technology is used to help address these challenges.

All of the conference presentations were recorded and the videos are available on YouTube at [https://www.youtube.com/user/code4lib/videos](https://www.youtube.com/user/code4lib/videos), they are worth checking out if you are interested in the topics that were presented.