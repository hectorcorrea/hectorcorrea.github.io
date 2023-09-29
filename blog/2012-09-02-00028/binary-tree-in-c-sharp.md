# Binary Tree in C#
A few months ago I decided write a C# program to handle binary trees. I was lucky to still remember some of the basics of this data structure from my college days and was able to write a program to handle the add operation and the code to "walk the tree" in a few hours. To make things more interesting I also decided to write a program to draw the binary tree on the screen. This turned out to be an interesting challenge and kept me busy for a few weeks.

The [source code for this program is here](https://hectorcorrea.com/downloads/DataStructures.zip). This version uses generics so it can be used with any type that implements the `IComparable` interface.

A binary tree, as indicated in [Wikipedia](http://en.wikipedia.org/wiki/Binary_tree), is a tree data structure in which each node has at most two children. To implement this data structure I created two classes: `BinaryTreeNode` and `BinaryTree`. The former is used to represent each node while the later is used to organize the nodes following the rules of binary trees. In addition, I created a third class `BinaryTreeDrawer` that implements the algorithm to actually draw a binary tree on the screen and let you *see* how the information is organized.

*A word of caution.* Although in production environments tree structures are used to optimize access to information the classes that I am providing here were not implemented with performance and optimization in mind. These classes are meant to showcase how binary trees are organized internally and allow people *to see them*.

The demo program allows you to add values to a binary tree and see how the tree looks graphically. For example, below is the graphical representation that it generates when the following values are added to a binary tree: 100, 50, 150, 25, 75, 12, 40, 60, and 90.

![binary tree image](https://hectorcorrea.com/images/binarytree2.png)

It is interesting to see how trees with random values look on the screen when hundreds of nodes are added to them. Click [here](https://hectorcorrea.com/downloads/binarytree200nodes.png) if you want to see a binary tree with 200+ random nodes (180 KB.)

With any luck I'll post an updated version in the next few weeks that will include the find and delete operation. My real goal is to post a program to represent **B-Trees** and **B+ Trees** in C# (which are the kings of the trees) and that a lot of people confuse with binary trees. Stay tuned.

Download the [source code](https://hectorcorrea.com/downloads/DataStructures.zip).


### Related posts

* [Drawing a Binary Tree in Ruby](https://hectorcorrea.com/blog/drawing-a-binary-tree-in-ruby/13) (2011)
* [Drawing a Binary Tree in CoffeeScript](https://hectorcorrea.com/blog/drawing-a-binary-tree-in-coffeescript/3) (2012)


