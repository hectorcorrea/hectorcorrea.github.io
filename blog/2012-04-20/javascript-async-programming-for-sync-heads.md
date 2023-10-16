# JavaScript Async Programming for Sync Heads

As I start learning Node.js one of the things that have puzzled me the most is having to write JavaScript code to run in an asynchronous fashion. Since Node.js uses an event-driven, non-blocking I/O model pretty much everything has to be written to run asynchronously. For somebody like me, that has always written code to be executed synchronously, the asynchronous paradigm takes a while to get used to.

This blog post compares **how a particular synchronous piece of JavaScript code looks and feels against against its asynchronous counterpart**. My gut feeling was that the asynchronous code would be much longer (in lines of code) and more complicated than the synchronous version but I wanted to see proof of it, well, at least as much proof as one single code example could provide.

I should clarify that I am, by no means, an expert on asynchronous programming or Node.js and the approach that I am showing in this blog post might not be optimal. I think it is a decent implementation but you should factor my newbie status on this kind of programming as you read this post.

The code that I describe in this blog is a function called **getTopicByUrl** which retrieves information for a blog entry. The steps to achieve this are as follows:

* Read a list of topics from a text file
* Find if the requested topic in the list
* Read the contents of the topic (from another file on disk)
* Create an object with the all topic data
* Return the topic data

In a typical **synchronous** implementation in JavaScript the pseudo-code for this function would look something like this:

```code
function getTopicByUrl(url) {
  topics = getAllTopics();
  topic = findTopicByUrl(url, topics);
  text = readTopicContent(topic.id);
  data = {id: topic.id, title: topic.title, content: text};
  return data;
}
```

To write this code in an **asynchronous** fashion the idea is to execute the first step but we don't wait for a return value. Instead we let the first step know what to do when it's done (i.e. what to function to execute to perform the second step.) Below is a pseudo-code implementation for this function:

```code
function getTopicByUrl(url, callback) {
  getAllTopics(step2);
  
  function step2(topics) {
    topic = findTopicByUrl(url, topics); // this is sync operation
    step3(topic);
  }

  function step3(topic) {
    readTopicContent(topic.id, step4);
  }

  function step4(topic, text) {
    data = {id: topic.id, title: topic.title, content = text};
    callback(data);
  }
}
```

By looking at these two examples we can easily see that the asynchronous code is larger than the synchronous version and, at least for the async newbie, much more complicated. 

Let's address these two issues separately.


## On the number of lines of code

What I found in a full blown implementation of this code (one with variables declaration, error handling and other areas of the code fully fleshed out) is that the asynchronous code is *not* much larger than the synchronous version. The following picture shows a complete implementation of this code. On the left side we have the synchronous version and on the right side we have the asynchronous one.

![Sync vs async](https://hectorcorrea.com/images/sync_vs_async.png)

The asynchronous code is barely four lines of code larger than the synchronous version (or about 10% longer.) A 10% code increase in lines of code is nothing to be proud of, but the asynchronous code was not 300% larger as the two pseudo code samples at the top of this blog post might have lead you to believe. This is something that I've noticed more than once. The asynchronous implementation tends to look larger than the sync version initially but once all the functionality has been implemented (error handling, variable declaration, conditional branching) the difference in the number lines of code is not that much.

Although I have yet to find an example in which the asynchronous code is shorter than the synchronous counterpart I've started to be less concerned about this since in the end the difference in the number of lines of code is not significant.


## On the complexity of the code

The second and more important part of the story has to do with code complexity. In the previous example the synchronous version of the code is definitively much simpler to read than the asynchronous counterpart. Regardless of how much experience you might have with sync or async programming I think is pretty clear that the synchronous version is easier to read since its sequential and there is no need to introduce the concept of callbacks.

Having said that, after writing asynchronous code for a little while the code does not look as daunting as it does the first time you read or write asynchronous code. **The asynchronous code is not as simple as the synchronous version but it's not rocket science either.**

The code shown in the picture has numbers indicating where each step is implemented on both versions.

Except for the very first step you can see on the picture how each of the steps on the synchronous version (step 2-5) lined up with its asynchronous counterpart. The very first step is defined at the bottom in the asynchronous version since it depends on the getTopicDetailsCallback being already defined. This looks and feel odd and I wish that wasn't the case but I could not find a way around it.


## A few observations on the code

The **asynchronous version relies on callbacks**. This is something that you don't have to deal with in synchronous programming but that you must work with when doing asynchronous programming. 

One of the callbacks that I defined (`getTopicDetailsCallback`) is *a function inside another function*. This is normal and common in JavaScript, but if you have never done this it might look strange initially. By having `getTopicDetailsCallback` defined inside `getTopicByUrl` it has access to variables defined in `getTopicUrl` which made sense since I wanted to re-use some values (like the `dataPath`) across multiple steps.

In the **synchronous version I have a single exit point** (line 78) regardless of whether the blog topic was found or not or whether there was an error reading the text of the blog topic. Since the code in this version will run sequentially I can setup stuff to be picked up in the following lines with no additional effort. In this case I initialized the data object with the appropriate information depending on whether there were any errors or not and then just return it to the called in line 78.

In the **asynchronous version however I have two exit points** (line 61 and line 77.) Since the code does not execute sequentially it was easier for me to bail out immediately in line 61 if a topic was not found and have another exit point in line 77 for the case where the topic was found and the call to `fs.readFile` was executed.

The thought of having more than one exit point for this case made me uncomfortable initially but I wonder if this is just because I am bringing "best practices" from the synchronous world into the asynchronous world.

The asynchronous version shows two ways to handle going from one step to another. To go from *step 1* to *step 2* we pass a **function name** to *step 1* so that it knows what to call when it's done. However, in *step 3* we define an **anonymous function** (line 65) that it will be executed when the process of reading the file has completed.

**Most of the techniques used in the asynchronous code are regular JavaScript code that most seasoned developers use on a daily basis but that would be new for casual JavaScript developers.** Having to do asynchronous programming in JavaScript will definitively push you to learn more about the language - and that's a good thing.


## Source code

~~The entire code for the project that I am using as my sandbox can be found on github at: https://github.com/hectorcorrea/simple-blog. File /models/blogsync.js has the synchronous implementation and /models/blog.js has the asynchronous implementation. The synchronous version is only for demonstration purposes and will not be maintained as the project evolves.~~


## Where to find more

Garann Means has a really nice (and short) [book on Node.js targeted to front end developers](http://www.amazon.com/Node-Front-End-Developers-Garann-Means/dp/1449318835) that it's worth to take a look at if you are new to Node.js.

If you are interested in learning more about asynchronous programming in JavaScript I highly recommend Trevor Burnham's [Async JavaScript: Recipes for Event-Driven Code](http://leanpub.com/asyncjs). The first chapter alone on "The JavaScript Event Model" is worth the price of the book. 


## Conclusions

As I make my way through the world of asynchronous programming in JavaScript and Node.js I am finding that the asynchronous code is usually slightly longer than it would be in a synchronous environment and a bit harder to understand. 

However, as I become more used to this new style the initial shock seems to fade away and the code starts to make sense. I think there are a lot of best practices that I have yet to figure out in the asynchronous world (just like I did over the years on synchronous programming) but that's just part of the learning process. The good thing is that there is a lot of information (books, web sites) that are available to help in this process as well as new tools and libraries are being developed to simplify the programming of asynchronous applications.

You might be wondering why bother? Why go through all this effort just to use an event-driven tool like Node.js. The truth is that I believe that **asynchronous programming is here to stay and will become the main way of developing applications**. You can see this today if you are developing applications with JavaScript, jQuery and using AJAX. If you are using Microsoft Silverlight you also have noticed that pretty much everything is done asynchronously. The same is true if you are looking into WebSockets (aka the next AJAX) since those calls are also asynchronous.

In my case since Node.js pretty much forces you to work in an asynchronous environment it helps me to make sure I stay on track as I jump head first into this brave new world of asynchronous programming.