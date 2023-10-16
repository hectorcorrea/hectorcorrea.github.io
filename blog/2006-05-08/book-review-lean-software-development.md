# Book review: Lean Software Development
A couple of weeks ago I finished reading [Lean Software Development](http://www.amazon.com/exec/obidos/ISBN=0321150783) by Mary and Tom Poppendieck. What a great little book! In this book Mary and Tom present several tools for lean (their term for agile) software development plus an excellent background on why these types of methods work.

Mary has a background (theory and practice) on manufacturing processes and in this book she describes how lean processes were/are successfully applied by companies like Toyota and 3M. I am usually disappointed when people try to compare software development to other industries (e.g. construction of buildings) because most of the times they don't have any background on the other field and make unfounded simplifications. Mary, on the other hand, has real life experience on manufacturing and software development and can compare between the two with realistic examples.

In this book the authors describe 7 lean principles, their rational, and how to apply them to software development:

* Eliminate waste
* Amplify learning
* Decide as late as possible
* Deliver as fast as possible
* Empower the team
* Build integrity 
* See the whole

Of these principles, **eliminate waste** is probably the most well known in agile software development and most people in our industry have come to accept it as a good think. Some other concepts, like **deciding as late as possible**, are typically not well understood by managers (and developers) and can be seen as incompatible with software development. Deciding as late as possible means that we don't need to make premature decisions about the software and we should keep our options open. This allow us to decide only *when we have enough information* about the system. For example, let's say that we are about to roll out version 1.0 of our system and we are not sure if we need to support "undo" in a specific feature. Instead of guessing and locking ourselves into the decision (possible an expensive one), let's roll the system out, see it in action, and *then* evaluate if we need this feature. Once the users use our system we might find that undo is actually needed but not in the way that we had anticipated. So instead of locking ourselves with a solution ahead of time, let's wait until we know enough about the problem. As the authors say, the key to be able to decide as late as possible is that we must build a capacity for change into the system. Unit testing and Test Driven Development are two good ways to account for this.

In addition, Mary and Tom provide with 22 thinking tools that can be applied in software development.

* Seeing Waste
* Value Stream Mapping
* Feedback
* Iterations
* Synchronization
* Set-Based Development
* Options Thinking
* Queue Theory
* and 14 others

Although a large part of the material presented in this book can be found in other books (e.g. [Agile Project Management with Scrum](http://www.amazon.com/exec/obidos/ISBN=073561993X) by Ken Schwaber and [Agile & Iterative Development](http://www.amazon.com/exec/obidos/ISBN=0131111558) by Craig Larman) this one includes comparison with other industries that are hard to find in other books. In addition, the authors provide the theory and motivation behind each lean/agile principle from a point of view that is not exclusive to software development. This makes the book an excellent guide for people involved in software projects without a software background (e.g. business owners.)