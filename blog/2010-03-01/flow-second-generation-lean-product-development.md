# Flow: Second Generation Lean Product Development
<img alt="The Principles of Product Development Flow" width="120" height="178" align="left" src="https://hectorcorrea.com/images/flowbook.jpg" />The last few weeks I've been reading the book [The Principles of Product Development Flow](http://www.amazon.com/Principles-Product-Development-Flow-Generation/dp/1935401009) by Donald Reinertsen<sup>[1]</sup> in which he describes what he calls the second generation of Lead Product Development.

I've been reading the book from the perspective of software development products and this review will highlight examples on that area. However Reinertsen's book is about product development in general (e.g. the development of a new drug or a new cell phone) and not software in particular.

Reinertsen describes a paradigm for product development that although it could be called Lean Product Development he chooses to call **Flow-Based Product Development** (Flow) to highlight the differences that exist between lean manufacturing and lean product development. 

Unlike *lean manufacturing* that primarily deals with predictable and repeatable tasks, homogeneous delay costs, and homogeneous task duration, in *lean product development* these situations are not the norm. In product development the tasks (almost by definition) are not repeatable and hardly ever predictable, delay costs are not homogeneous, and tasks duration have very high variations.

In the case of software development it's not uncommon for teams to be dealing with problems that they have never solved before (e.g. writing a brand new system) or and with technologies that are new to the team. Likewise, the cost of delaying the implementation of a new feature versus the cost of fixing a specific bug can be widely different.

In chapter one Reinertsen starts by describing what he calls twelve critical problems with traditional product development orthodoxy including: Failure to Correctly Quantify Economics, Blindness to Queues, Worship of Efficiency, Hostility to Variability, Institutionalization of Large Batch Sizes, Underutilization of Cadence, Managing Timelines instead of Queues, Absence of Work-In-Progress Constraints, and Centralized Control.

In the remaining chapters of the book Reinertsen presents eight ideas/themes that he suggests to address these problems.

* Economics
* Queues
* Variability
* Batch Size
* WIP Constraints
* Cadence, Synchronization, and Flow Control
* Fast Feedback
* Decentralized Control

In software development some of these ideas are already popular thanks in large to the wide spread adoption of agile software development processes. For example Scrum recommendation of short sprints helps reduce *batch size*. Likewise, the daily collaboration between developers and QA during the sprint as well as frequent product demonstrations advocated by Scrum allow for *fast feedback* to take place.

However, some of the other ideas that Reinertsen presents on his book (like Economics, Queues, WIP Constraints) are not mainstream in software development yet. Some of the new development processes like Kanban<sup>[2][3]</sup> are making strides in these areas and this book helps to clarify why these changes are important in product development in general. 


## On Economics

On the topics of economics, Reinertsen starts with the premise that "we do product development to make money" (p.15) and yet most product development teams cannot correctly quantify economics on their products. In his view product developers currently focus on proxy variables to calculate economic impact rather than using the real economic objective which he calls <strong>life-cycle profits</strong> (p.29)

For example, a typical proxy variable is cycle-time. Most developers typically try to optimize cycle-time and make it shorter. On the surface this sounds all good. However, few product developers can correctly calculate how much a delay on cycle-time will actually cost in terms of life-cycle profits.

An example of this in software development is when a team cannot objectively calculate the value (in terms of profit) of working on feature A or feature B. If the team does not know the impact to the bottom line, How can they make a decision that makes the more sense for the company? Without knowing the real value of two features, the team might decide to work on feature A because it's more appealing or because the developers needed for feature B are currently busy wrapping something else. However, if the team knew that feature B will bring four times more profit to the company if delivered first they might act different and rearrange the team to complete feature B before feature A.

Reinertsen points out that having a common unit of measure solves many problems as it allows the decisions on what makes more sense to take place on an even keel rather than using the unit of measurement that is popular at the moment. The common measure that he proposes is profits.

For example, given two choices to implement a feature a team might decide to rush it and release it to market rather than use a more comprehensive approach and wait a few months. Either one of these choices could be the correct one at different points in the life cycle of a product. However, the right choice depends on the economics of this decision at a given point in time. Will the company make more money rushing the feature and being first too market or will it make more money delaying its implementation and doing a more comprehensive implementation? What about the "technical debt" that the team will incur by rushing a feature? Does this cost outweigh the benefits of being first to market? All of these things should be considered and measured with the same unit.

Reinertsen indicates that on his research teams tend to *think* that they know the economics of their decisions. However,  when asking different people on the team to quantify (in economic terms) the value of the team decisions their responses vary on a 50 to 1 ratio. When the answers are in such wide range it's obvious that the team is not using a common unit of measurement.

Chapter two contains nineteen principles to help product developers make good decisions based on economics. Although Reinertsen does not provide the "silver-bullet" to make economic decisions the principles that he provides are a good place for product developers to start focusing including paying attention to Cost of Delay (p. 31), U-Curve Optimizations (p.35), Economics Trade-off  (p. 37), Marginal Cost and Marginal Value (p.45).


## On Queues and Batch Size

One of the areas where lean manufacturing is very different from product development is in the management of queues. In lean manufacturing the problem with queues is well understood and has been optimized for many years. Entire manufacturing books have been written on how to manage bottlenecks on assembly lines to prevent inventory accumulation  (e.g. [The Goal](http://www.amazon.com/Goal-Process-Ongoing-Improvement/dp/0884270610) by Eli Goldratt.) However, there is little literature in software development that addresses the effects of work in progress (which is the software equivalent to inventory.) An example of work in progress in software development are features developed but not yet in production either because they are under development or developed but still being tested.

One of the reasons work in progress (WIP) is so poorly managed in product development is because it's almost invisible. In manufacturing you can walk around a plant and see work in progress lying on the floor. Although work in progress does not sits around on the shop floor in software development work in progress can be easily identified by looking at queues: queues of items ready to be coded, items ready to be tested, items ready to be released, and so on. 

You might be wondering, so what? Why should I care about queues in software development? Although there is no cost associated with *storing* work in progress in software development Reinertsen indicates that work in progress cause several unwanted side effects in product development including: **Longer Cycle Time, Increased Risk, More Variability, More Overhead, Lower Quality, and Less Motivation**.

An example of this in software development is a queue of features coded but not tested. The larger the queue of features coded but not tested the larger the test cycle that the product will need to go before the features can be released. A large number of features to be tested and then released also translate in higher risk on the new version of the system.

One of the principles of queue theory is called Little's Formula. In layman terms Little's Formula says "average queue size determines average cycle time" (p. 112.) This means that you can reduce cycle time by reducing batch size. This is important as it means that before we go looking for complex solutions to reduce long cycle times (like adding more staff or looking for bottlenecks) we can start by reducing the batch size. Software teams that are already using Agile methods like Scrum have experienced this with the use of short Sprints (e.g. 2-week long cycles) rather than the old-fashion multi-month development cycles.

On his book Agile Management for Software Engineering<sup>[4]</sup>, David Anderson gives a comprehensive explanation of how inventory and lead time apply to software and it's another good reference to learn more on this subject.


## On WIP Constraints

After making you aware of Queues and the importance of Batch Size, Reinertsen moves to Work In Progress Constraints (or WIP Constraints) and why they are important in product development. Reinertsen indicates that while limiting batch size helps by nature to reduce the amount of work in progress, it does not address the fundamental problem of random variations in flow common to product development (p. 143) As Reinertsen indicates at the beginning of his book, product development is subject to many variations not common in lean manufacturing. In the Chapter 6 Reinertsen describes ideas and principles applied in telecommunication networks to deal with high variability and how they can be applied to product development. Instead of using a Theory of Constraints as a manufacturing book probably would, Reinertsen describes Kanban systems instead.

There is an entire movement in the software development industry to raise awareness of the problems that work in progress (WIP) causes to software processes. You can take a look at their work at [http://www.limitedwipsociety.org/](http://www.limitedwipsociety.org/) and also a new book in the works on Kanban for Technology Organizations<sup>[2]</sup> by David Anderson.


## In Summary

All in all I found Donald Reinertsen book very interesting and easy to read. Although the book is not specific to software development most of the concepts described on it apply to software development and would allow you to understand why the recommendations of agile, lean, or pull methods work and are important.


### References

* [1] *Principles of Product Development Flow, Second Generation Lean Product Development*; Donald Reinertsen; Celeritas Publishing; 2009.
* [2] *Kanban: Successful Change Management for Technology Organizations*; David Anderson; Unpublished manuscript as of November 2009.
* [3] See [http://www.kanban101.com](http://www.kanban101.com/) for a quick introduction to Kanban
* [4] *Agile Management for Software Engineering, Applying Theory of Constraints for Business Results*; David Anderson; Prentice Hall; 2003.