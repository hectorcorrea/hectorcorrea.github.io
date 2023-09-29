# Drawing a Binary Tree in Ruby
<img src="https://hectorcorrea.com/images/binarytree_simple.png" alt="git" align="left"/>When I started learning Ruby last year I decided to implement a **binary search tree** and some of its basic operations (insert, delete, walk, and search) just to get my feet wet on the language. Binary search trees are a good exercise because you need to use several features of the language like conditional statements, loops, and classes while at the same time you are solving an interesting problem. The algorithm for binary search trees is well documented in most introductory books on algorithms and on the web. 

To make things a bit more challenging I also implemented a program to **draw the binary search tree** so that people can see how a binary search tree looks like when it has more than just a handful of nodes. In this particular case I wrote small a web application in Ruby that draws the binary search tree using the new canvas element supported in HTML 5. You can download the entire source code for the binary tree and the demo web site from [this GitHub repo](https://github.com/hectorcorrea/binary-tree) and play with it in your local environment.

This blog post gives short overview of the main methods to implement the binary search tree, the algorithm to draw the binary search tree, and the web site to demo the drawing algorithm.


## Binary Tree or Binary Search Tree

Properly speaking the code in this blog post implements a Binary Search Tree (BST) which is a more specific version of a Binary Tree. Cormen et al. give a good explanation of this on their book [Introduction to Algorithms](http://www.amazon.com/Introduction-Algorithms-Second-Thomas-Cormen/dp/0262032937). Binary trees are trees in which each node has at most two children, one of them called "left subtree" and the other called "right subtree" but there is no order enforced per-se (pp. 1178). Binary Search Trees on the other hand are always stored in a way as to satisfy the binary-search-tree property (pp. 287):

> Let x be a node in a binary search tree. 
> If y is a node in the left subtree of x, then y.key ? x.key. 
> If y is a node in the right subtree of x, then y.key ? x.key.

I took a bit of a liberty on this blog post (and its related source code) and use both terms interchangeably. Please be aware that all of the references to *binary tree* in this blog post are in fact references to *binary search trees*.


## Implementing a Binary Search Tree

The procedures to implement the basic binary search tree operations like insert, delete, search, and walk are well documented in most introductory books on data structures and algorithms and on the web (e.g. [www.algolist.net](http://www.algolist.net/Data_structures/Binary_search_tree) and [wikipedia](http://en.wikipedia.org/wiki/Binary_search_tree)) and they are fairly easy to implement in Ruby.

The class that I implemented can be used as follows ([gist](https://gist.github.com/hectorcorrea/911769)):

```code
require "binarytree"
tree = BinaryTree.new(40)
tree.add(30)
tree.add(100)
tree.add(20)
tree.add(35)
tree.add(25)
tree.add(34)
puts "Tree nodes: #{tree.to_s}" 
=> "20, 25, 30, 34, 35, 40, 100"
```

Out of the box we can use this BinaryTree class with numbers or strings or any other native Ruby type. We can also use it with other types as long as they implement comparable operations (i.e. def <=>).


### Adding Nodes to a Binary Search Tree

The code to add nodes to the binary search tree is shown below. This code first evaluates if we already have a root node, if we don't then we create a root node with the new value and we are done. If we already have a root node then scan the nodes of the tree (using the binary-search-tree property aforementioned, smaller values go to the left, equal or greater than values go to the right) until we reach a leaf node. Once we've reached a leaf node we update it to point to the new node. ([gist](https://gist.github.com/hectorcorrea/909230))

```code
def add(new_value)
 
  if @root == nil
    @root = Node.new(new_value)
    @total_nodes = 1
    return
  end
 
  current = @root
  while(true)
    if new_value <= current.value
      if current.right == nil
        current.right = Node.new(new_value)
        break
      else
        current = current.right
      end
    else
      if current.left == nil
        current.left = Node.new(new_value)
        break
      else
        current = current.left
      end
    end
  end
 
  @total_nodes += 1
end
```


### Walking the Tree

One of the advantages of a binary search tree is that it's very easy to retrieve *in order* the values stored on it. This process is called "walking the tree in order". For example, if we have a tree with the following values: 100, 50, 200, and 150 walking the tree in order will give us: 50, 100, 150, and 200. Walking the tree can be implemented easily with a recursive function. However the recursive method, although elegant, it's not very efficient when the tree is expected to have a large number of nodes. The code that I implemented to walk the tree does not use recursion but instead uses an array as a stack to keep track of the nodes that it visits. This version is not as elegant as the recursive version found in most introductory books but it works well even when the tree has thousands of nodes.

Below is the code that I implemented for walking the tree in order ([gist](https://gist.github.com/hectorcorrea/911788)).

```code
def walk
 
  return nil if @root == nil
  node = @root
  stack = []
  ignore_left = false
 
  while(true)
 
    #p "processing #{node.value.to_s}"
    if ignore_left
      #p "ignoring nodes to the left"
      ignore_left = false
    else	      
      if node.left != nil
        #p "moving to the left"
        stack << node
        node = node.left
        next
      end
    end
 
    yield node
 
    if node.right != nil
      #p "moving to the right"
      node = node.right
      next
    end
 
    break if stack.length == 0
 
    #p "popping from the stack"
    node = stack.pop
    ignore_left = true
  end
 
end
```

While implementing this method I got to experience first hand the beauty and simplicity of one of Ruby's most touted features: blocks. You can see the use of blocks in the line `yield node` of the previous code. As the algorithm finds the next node to process it simple *yields it* to the caller program. This allows the code to walk the tree to be completely independent from the action that we want to perform on each node. For example, we can print each node to the console with the following code:

```code
tree.walk { |node| puts "#{node.value}" }
```

In this example the block is a simple `puts` statement but we'll see a more sophisticated example of this when we review the code to draw the binary search tree. This kind of decoupling between the iteration of the elements in the tree and the action to execute on each of them can be implemented in other programming languages via delegates or pointers but I was amazed on how simple and elegant it was to do this in Ruby.


## How to Draw a Binary Search Tree

The algorithm to draw a binary search tree that I implemented is pretty much a variation of the algorithm to walk the tree with the exception that we need to calculate the coordinates where each node needs to be placed as we traverse the nodes in order. Calculating the coordinates turned out to be an interesting challenge but a final simple solution.

The basics of this algorithm are as follow:
* Draw the root node in the coordinates indicated.
* Draw the left subtree to the left of the root node.
* Draw the right subtree to the right of the root node.

```code
def draw_node(root, x, y, block)
    return if root == nil
    block.call(root, x, y, x, y)
    draw_left(root.left, x, y, block) if root.left != nil
    draw_right(root.right, x, y, block) if root.right != nil
end
```

To calculate the coordinate where the *left subtree* should be drawn we count how many children the *right side* of the *left subtree* has. We use this number to calculate the x-coordinate as a negative offset from the root. The y-coordinate is simple a positive offset from the root. Then we recurse this process for the left subtree and right subtrees. The following code snippet shows this code ([gist](https://gist.github.com/hectorcorrea/911977)).

```code
def draw_left(node, parent_x, parent_y, block)
 
    if node.right != nil
      count = 1 + children_count(node.right)
    else
      count = 0
    end
 
    x = parent_x - @x_distance - (count*@x_distance)
    y = parent_y + @y_distance
    block.call(node.value, x, y, parent_x, parent_y)
    
    draw_left(node.left, x, y, block) if node.left != nil
    draw_right(node.right, x, y, block) if node.right != nil
end
```

The procedure to calculate the coordinate where the *right subtree* should be drawn is just the reverse: we count how many children the *left side* of the *right subtree* has. We use this number to calculate the x-coordinate as a positive offset from the root. The y-coordinate is simple a positive offset from the root. Then we recurse this process for the left subtree and right subtrees. The following code snippet shows this code ([gist](https://gist.github.com/hectorcorrea/911978)).

```code
def draw_right(node, parent_x, parent_y, block)
 
    if node.left != nil
      count = 1 + children_count(node.left)
    else
      count = 0
    end
 
    x = parent_x + @x_distance + (count*@x_distance)
    y = parent_y + @y_distance
    block.call(node.value,x,y, parent_x, parent_y)
    
    draw_left(node.left, x, y, block) if node.left != nil
    draw_right(node.right, x, y, block) if node.right != nil
end
```

Like in the walk method, the three draw methods presented here only calculate where the node should be drawn but they don't actually draw the node. Instead they call a block with the appropriate parameters to perform the actual operation. You can see this in the line `block.call(node.value, x, y, parent_x, parent_y)` in these three routines.

The following code shows an example of calling the draw method on a simple tree and displaying on the console the coordinate where each of the nodes should be drawn ([gist](https://gist.github.com/hectorcorrea/911989)):

```code
require "binarytree"
require "binarytreedrawer"
 
tree = BinaryTree.new
tree.add(100)
tree.add(50)
tree.add(150)
tree.add(25)
tree.add(75)
tree.add(125)
tree.add(175)
 
drawer = BinaryTreeDrawer.new(tree)
drawer.draw(0,0, Proc.new { |value, x, y, px, py| 
  puts "Value #{value} at (#{x}, #{y})"
})
 
=> Value 100 at (0, 0)
=> Value 50 at (-40, 30)
=> Value 25 at (-60, 60)
=> Value 75 at (-20, 60)
=> Value 150 at (40, 30)
=> Value 125 at (20, 60)
=> Value 175 at (60, 60)
```

There is plenty of research on the best way to draw binary search trees with lots of data. The book [Graph Drawing: Algorithms for the Visualization of Graphs](http://www.amazon.com/Graph-Drawing-Algorithms-Visualization-Graphs/dp/0133016153) by Tollis et al. seems to be *the* reference that most research papers and presentations cite. On pages 43-45 they provide a more sophisticated version of the algorithm that I used plus several advanced algorithms for different scenarios.

Perhaps in the future I'll update my code to implement the enhancements suggested by Tollis et al. and also to remove the recursive calls from my implementation.


## A Real Example of Drawing a Binary Search Tree on a Web Page

Once we have the coordinates where each node can be drawn we could use any graphic program to perform the actual drawing. Since I wrote this program as learning exercise I decided to write a small Ruby web application to do the actual drawing. In this web application I use the new [HTML 5 canvas element](http://diveintohtml5.org/canvas.html) as the drawing surface and a series of JavaScript calls to draw lines and circles on the appropriate coordinates.

The following code shows a simplified version of how I generate the JavaScript calls to draw the nodes (circles and labels) and lines connecting the nodes ([gist](https://gist.github.com/hectorcorrea/912001)):

```code
drawer = BinaryTreeDrawer.new(@tree)
drawer.draw(0, 0, Proc.new { |value, x, y, px, py| 
 
  circles << "draw_circle(centerX + #{x}, offsetY + #{y}, 5);" 
  lines << "draw_line(centerX + #{x}, offsetY + #{y}, centerX + #{px}, offsetY + #{py});"
  labels << "draw_label(centerX + 7 + #{x}, offsetY+5+#{y}, \"#{value}\");"
  
})
```

Notice that this code merely creates three arrays (circles, lines, labels) with some JavaScript calls. These arrays are later inserted on a web page so that when the user renders it the tree is drawn on their browser.


## Source Code

You can download the entire source code for the binary tree and the demo web site from [this GitHub repo](https://github.com/hectorcorrea/binary-tree) and run it in your local environment. The code allows you to generate binary trees with either random values or your own data and see how they are drawn.

On my laptop I've drawn trees with up to 10,000 nodes. One of the disadvantages of the drawing algorithm that I implemented is that it's not an area-efficient algorithm which leads to very wide drawings. You can see this effect in this [tree with 500 nodes](https://hectorcorrea.com/downloads/tree500.png). 

A more dramatic example is a tree with 5,000 nodes that I generated with this code too. If I were to print this tree it would be 115 feet long by 1 foot tall (as a reference, a basketball court is 94 feet long!) You can download this tree from *https://hectorcorrea.com/downloads/tree5000.png* but be aware that it's almost 4MB in size and it might crash your browser when trying to display it, which is why I am not making it a hyperlink. On a Mac, the PNG renders OK on Safari but FireFox seems to have problems with it.


### Related Post
* [Drawing a Binary Tree in CoffeeScript](https://hectorcorrea.com/blog/drawing-a-binary-tree-in-coffeescript/3) 
