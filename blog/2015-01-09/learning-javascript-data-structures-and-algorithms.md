# Learning JavaScript Data Structures and Algorithms
<img src="https://hectorcorrea.com/images/js_book_thumb.jpg" style="float:left;" /> The folks at Packt Publishing asked me to review one of their newest books [Learning JavaScript Data Structures and Algorithms](https://www.packtpub.com/application-development/learning-javascript-data-structures-and-algorithms) by Loiane Groner.<sup>[1]</sup>

As a fan of data structures and algorithms I am glad to see a book on the subject targeted at JavaScript developers. The publication of this book is rather timely given the tremendous growth in popularity that JavaScript has experienced over the years, not only as *the* language for front-end web development, but also recently as a viable alternative for backend development via Node.js. Although there are a lot of books on data structures and algorithms, most of them are targeted to Java/C developers, and there is very little on the subject for JavaScript developers. As Groner indicates<sup>[2]</sup> "who said data structures and algorithms were only made for languages such as C and Java" [p. 37]

In this book Groner describes some of the basic data structures and algorithms that most books in this category typically cover including Stacks, Queues, Lists, Trees, Graphs, Sorting and Searching and provides clear and simple implementations for them in JavaScript. Many developers would benefit from reading this book, but particularly those who started their software development career with JavaScript (or other scripting language) and haven't taken traditional computer science degree courses where data structures and algorithms are typically taught.

Although there are already other good beginner-level books on data structures and algorithms (see <sup>[3]</sup> and <sup>[4]</sup> for example) they tend to be targeted at people studying a computer science degree with the help of a professor in a traditional classroom setting. Groner's book on the other hand is a gentler introduction to the subject and I would assume a better fit for those learning data structures and algorithms for the first time on their own. Given that I have a computer science degree and have been writing code for over two decades I might not be the best judge of this, but I found Groner's book much easier to follow than other introductory books<sup>[5]</sup>.

Groner does a great job of describing the data structures, the mechanics of the algorithms, and provides clear and concise JavaScript implementations. Each chapter begins with a short introduction to the terminology needed to understand the data structures and algorithms presented. This is very important as the complexity of the material increases on each new chapter and yet she manages to keep both, the explanations and code implementation, simple and accurate.

Groner wrote the examples so that they can be run on any modern browser with little or no setup required from the reader. She also provides instructions on how to setup a simple web server to have a better experience navigating through the different examples on each chapter. I think this painless setup will appeal to a lot of readers that tend to shy away from heavy server-side development. The samples can also be easily be adapted to run server side via Node.js for those inclined to do server-side development with JavaScript, though.

All of the code examples presented in the book are available online at this GitHub repo: [https://github.com/loiane/javascript-datastructures-algorithms](https://github.com/loiane/javascript-datastructures-algorithms).


## The basics
<img src="https://hectorcorrea.com/images/js_queue_thumb.jpg" style="float:left;" /> The book starts with an introduction to JavaScript and a tour of the features of Arrays in JavaScript. I appreciate that Groner spends a full chapter on Arrays given that in JavaScript they come with a lot of built-in functionality typically not found in other languages (e.g. slice, map, reduce, filter, shift); this chapter also explains some of the nuances using arrays in JavaScript (e.g. sort is by default lexicographical). 

The first two data structures presented in the book are **Stacks** and **Queues** and they are implemented via arrays. Using arrays for these data structures allowed Groner to keep the examples concise and keep the focus on the algorithms themselves (e.g. FIFO vs LIFO) and implement some variations (like Priority Queue and Circular Queue) relatively easily. 

I appreciate how clear and concise most of the code examples throughout the book are. For example, in barely 30 lines of code<sup>[6][7]</sup> Groner shows how to implement a Queue in JavaScript. Below is a snippet of this code:

```code
// Queue implementation (partial)
function Queue() {
  var items = [];

  this.enqueue = function(element){
    items.push(element);
  };

  this.dequeue = function(){
    return items.shift();
  };

  this.print = function(){
    console.log(items.toString());
  };
}

// Using the queue
var q = new Queue();
console.log(queue.isEmpty()); 
q.enqueue("Hello");
q.enqueue("World");
q.print();
q.dequeue();
```

After presenting Stacks and Queues Groner moves to more complex data structures like **Linked Lists**, **Sets**, **Dictionaries** and, **Hashes**. I liked how smoothly Groner reused data structures implemented in previous chapters. For example, on chapter 7 when implementing a technique to handle collisions in a HashTable she uses the LinkedList implementation that she presented on Chapter 5 as shown in this code snippet [p. 237]. 

```code
// original implementation
this.put = function(key, value) {
  var position = loseloseHashCode(key);
  table[position] = value;
};

// improved version
this.put = function(key, value) {
  var position = loseloseHashCode(key);
  if (table[position] == undefined) {
    table[position] = new LinkedList();      
  }
  table[position].append(new ValuePair(key, value)); 
};
```

I enjoyed that Groner consistently covers the basic implementations of most algorithms and in some cases even discusses interesting variations. For example, in addition to the basic Linked List she touches on Double Linked lists and Circular Linked Lists. Likewise, the chapter on Dictionaries and Hashes not only describes the basis of them but also how to handle collisions and how to implement a Hash Table. 


## Trees and Graphs
<img style="float:left" src="https://hectorcorrea.com/images/js_tree_thumb.jpg" />The last two data structures that Groner explains in her book are **Trees** and **Graphs**. The code implementations for these chapters are of course more complex than previous ones but Groner manages to keep the code clear and simple. She uses recursive implementations for most of the operations of these chapters to keep the code concise. Although recursive implementations don't always work with large loads, given that the goal of the book is to introduce the concepts to beginners rather than stress test the algorithms, the use of recursive implementations makes sense<sup>[8]</sup>.

I was glad to see that Groner explains and implements all of the basic operations for Trees, *including the complex ones* like removing a node from a tree, to give a comprehensive overview of the algorithms. She also explains the basic traversal operations for binary trees (in-order, pre-order, post-order) and provides guidance on other kinds of trees that readers should explore should they want to learn more about this subject. The same is true for Graphs. Groner does a nice job explaining some of different strategies that can be use to implement them (e.g. via an adjacency matrix, an adjacency list, or an incident matrix) and when each implementation is best suited. 

As mentioned before, Groner takes advantage of data structures presented and implemented in previous chapters to implement more complex ones. Below is a fragment of the code to implement a Graph and the *add vertex* and *add edge* operations. Notice how she takes advantage of the Dictionary to store the adjacency list. The full implementation is 170+ lines of code and it's available [on the GitHub repo](https://github.com/loiane/javascript-datastructures-algorithms/blob/master/chapter09/01-Graph.js).

```code
function Graph() {
  var vertices = []; 
  var adjList = new Dictionary();
      
  this.addVertex = function(v){
    vertices.push(v);
    adjList.set(v, []); //initialize adjacency list with array as well;
  };

  this.addEdge = function(v, w){
    adjList.get(v).push(w);
    adjList.get(w).push(v);
  };
}
```

Groner also explains two common Graph traversal algorithms: **Bread-First Search** and **Depth-First Search** and their implementations in JavaScript. These very important topics and not easy to explain but she manages to describe them in good detail along with basic code implementations that you can run to see the algorithm in action. 


## Searching and sorting
The last chapter of the book is dedicated to searching and sorting. Groner introduces the reader to some basic sorting algorithms (i.e. bubble sort, merge sort, insertion sort, quick sort) and their implementation in JavaScript. I found it odd that there was no explanation on how these algorithms have different execution times depending on how the data is sorted at the beginning, though.

In this chapter Groner also presents the binary search algorithm. Although the description of the algorithm and its implementation were accurate, I felt like this section could have been better connected to the binary search tree explored in the previous chapter. There is a note on this chapter linking the two but, given that this book is for beginners, I would have liked to see a bit more explanation on their different usages (e.g. sorting data as it is inserted versus sorting after the fact.) 


## Bonus Chapter
There is also a free online extra chapter titled [More About Algorithms](https://www.packtpub.com/sites/default/files/downloads/4874OS_Chapter11_More_About_Algorithms.pdf) in which Groner covers in some detail concepts that are mentioned throughout the book like recursion, Fibonacci, and big-O notation. This chapter is mentioned on the eBook [p. 26], but it is not well advertised on the PacktPub's web site. It's worth checking out if those concepts are new to you.


## Criticism
Although the content of the book is excellent and the code implementations for the algorithms seem accurate, I felt that this book could have used a bit more editing before going to press. Throughout the book I ran into some sentences that seem to have been dropped in the middle without much context or connection to the rest of the book. I was a bit surprised by this lack of editing given that there are several reviewers listed on this book so I suspect that they focused on the accuracy of the technical implementations (as they should) rather than making the book feel more polished.

Another area that I would have liked to see improved is the visualization of the algorithms. The code provided outputs the results to the debug console. I think it would have been nicer if the output was to a web page, yet I also realize outputting to the debug console via `console.log()` keeps the samples more concise. Likewise, the code as-provided needs to be tweaked in order to run in Node.js. It would have been nice if the code were provided in a way that could be run unchanged on both the browser and the server. 

Overall these are minor criticisms on the book. As I indicated above, the content and the explanations are excellent for an introductory level book as are the JavaScript implementations provided.


## Summary
As a computer science student in college I found data structures and algorithms a fascinating topic. Many years later, as a practitioner, I still find them fascinating and intellectually challenging<sup>[9]</sup>. Although most of us will never be asked to implement a particular algorithm like the ones described in this book, I have always seen them as a great way to learn different programming techniques and keep our development skills sharp. 

I think Groner's book is a great introductory book to the subject and I am glad to see authors focusing on JavaScript developers that might have never been exposed to this topic. Even those that have been exposed to these concepts before (and found them a bit dry) will find Groner's book good way to revisit these concepts and have fun implementing them.


### References
[1] Disclaimer: Packt Publishing gave me access to a free copy of the eBook for me to write this review. I did not receive any other compensation for it.
[2] Given that I used an eBook to write this review, please note that page numbers might be different in other formats. To facilitate their location I also indicate the chapter in which they appear.
[3] [Introduction to Algorithms](http://www.amazon.com/gp/product/0262033844) by Thomas H. Cormen et. al. 
[4] [Algorithms](http://www.amazon.com/Algorithms-4th-Edition-Robert-Sedgewick/dp/032157351X) by Robert Sedgewick and Kevin Wayne 
[5] O'Reilly [recently published another book](http://www.amazon.com/Structures-Algorithms-JavaScript-Michael-McMillan/dp/1449364934) with almost the exact same title and table of contents but I have not read it so I cannot compare the two.
[6] Full Queue implementation at [https://github.com/loiane/javascript-datastructures-algorithms/blob/master/chapter04/01-Queue.js](https://github.com/loiane/javascript-datastructures-algorithms/blob/master/chapter04/01-Queue.js)
[7] Full example at [https://github.com/loiane/javascript-datastructures-algorithms/blob/master/chapter04/02-UsingQueues.js](https://github.com/loiane/javascript-datastructures-algorithms/blob/master/chapter04/02-UsingQueues.js)
[8] In fact, on page 4 of [the bonus chapter 11](https://www.packtpub.com/sites/default/files/downloads/4874OS_Chapter11_More_About_Algorithms.pdf) she explicitly says that although recursive implementations might be slower than non-recursive ones they are easier to understand and require less code.
[9] Shameless plug. Over the years I've posted on my blog examples on how to implement and draw Binary Search Trees on [Ruby](https://hectorcorrea.com/blog/drawing-a-binary-tree-in-ruby/13) and [CoffeeScript](https://hectorcorrea.com/blog/drawing-a-binary-tree-in-coffeescript/3).
