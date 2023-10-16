# Functional Asynchronicity Explained
Over at the Pragmatic Bookshelf Trevor Burnham has a great post titled [A CoffeeScript Intervention](http://pragprog.com/magazines/2011-05/a-coffeescript-intervention) in which he shows several code snippets that demonstrate how CoffeeScript code is typically smaller and cleaner than the equivalent JavaScript code.

In particular I love the example under the **Functional Asynchronicity** section. In this section he shows a piece of code that trips most new JavaScript developers.

```code
for (var i = 1; i <= 3; i++) {
  setTimeout(function() { console.log(i); }, 0);
}
```

When this code is executed it will output `4, 4, 4` to the console rather than `1, 2, 3` as most people guess the first time they work with JavaScript.

Although in his original blog post Trevor explains the reason for this behavior, in this blog I would like to expand a little bit more on *the reasons* why we get `4, 4, 4` rather than `1, 2, 3` since I believe they are fundamental to both understand and appreciate CoffeeScript.

There are two reasons why we get `4, 4, 4` in the previous code. The first reason is related to the way JavaScript’s `setTimeout` function works. The second reason has to do with the way JavaScript picks the value of `i` when the `console.log(i)` line is finally executed.


## Understanding setTimeout in JavaScript

In JavaScript the [setTimeout()](http://www.w3schools.com/js/js_timing.asp) function is used to execute a piece of code *sometime in the future*. In his book [CoffeeScript: Accelerated JavaScript Development](http://www.amazon.com/CoffeeScript-Accelerated-Development-Trevor-Burnham/dp/1934356786) (p. 108) Trevor sums it up like this:

> setTimeout always adds its target to the “event queue,” 
> which isn’t invoked until after all the other code has run.

In our particular example this means that the `function() { console.log(i); }` (aka the target) will be queued up and executed when the for loop has ended -- not on each iteration.

This is true regardless of the timeout value used. Since we used a timeout value of 0 ms the target functions would be executed immediately after the loop has ended. If we had used a value of 1000 ms they would be executed 1000 ms after the loop ended. *The important thing to recognize is that the targets won’t be executed until after the loop has ended*.

This [thread in stackoverflow.com](http://stackoverflow.com/questions/4574940/settimeout-with-zero-delay-used-often-in-web-pages-why) gives a great overview on deferred execution in JavaScript and it’s worth a look if you are not very familiar with this concept.


## The value of "i"

Now that we know that the calls to `console.log` won’t happen until after the loop has ended, we need to understand how does JavaScript determine what value of `i` would be used at that time.

We know that at the end of the loop the value of `i` would be `4`. That’s easy enough to understand. However, what most JavaScript newcomers fail to grasp is that when we create an inner function that references an existing variable, the inner function will actually have access to the original variable when is eventually executed. Let’s spell this out.

The first thing to notice is that *we are not passing the value of “i” to the setTimeout function*. Instead *we are creating and passing a function to setTimeout*.

Secondly, the function that we are creating and passing to `setTimeout` references a variable `i` which exists in the context in which we are creating it. This is a key concept called **closure** and not found in most programming languages.

In his book [JavaScript: The Good Parts](http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742) p. 38) Douglas Crockford defines closure as:

> the ability of a function to access the context in which it was created

In our case the new function (which just outputs the value of `i` to the console) was created in a context that has an `i` variable defined and therefore *the new function will have access to the value of “i” whenever it is executed*. Notice that this is very different than assuming that the new function will have the value of `i` at the time the new function was created!

We know from our review of `setTimeout` that the target (i.e. the new function that outputs `i` to the console) won’t be executed until after the for loop ends and that time the value of `i` will be `4`. This explains why we got `4, 4, 4`.

On his book Crockford also has a great example showing how a variable lingers after the original function has completed if the variable is referenced by any of the inner functions. In fact the whole context in which the inner function was created is what lingers. Definitively worth reading if you are not familiar with closures in JavaScript.


## Yeah, but that’s still not what I wanted...

So now that you understand why JavaScript outputs `4, 4, 4` in our example rather than `1, 2, 3` you are probably wondering but how can I change this behavior. 

Basically what you need to do is create a new context for the `function() { console.log(i); }` and make sure that in this new context the value of `i` is locked to the value of the iteration (1, 2, 3.)

Here is how Trevor does this on his blog post:

```code
for (var i = 1; i <= 3; i++) {
  (function(i) {
    setTimeout(function() { console.log(i); }, 0);
  })(i);   // execute it!
}
```

In this example a new anonymous function with an `i` parameter is created and executed immediately on each iteration of the loop. Notice the `(i);` in line number 4 means we are executing it.

When this new anonymous function is executed on each iteration it calls `setTimeout` and creates a new function to output the value of `i` to the console - just as we did before. However, in this case the context in which the `console.log` will be executed will see the `i` defined as a parameter to this anonymous function -- not the `i` in the for loop!

In other words, the context in which the `function() { console.log(i) }` is created is now `function(i) { … }` rather than the original for loop. Therefore when `console.log(i)` runs it will pick the value of `i` from this new context and this new context receives a single value on each iteration.

Below is a slightly modified version of Trevor's code. In this new version I’ve renamed the variable `i` in the for loop to `x` to make it easier to see what we are trying to do:

```code
for (var x = 1; x <= 3; x++) {
  (function(i) {
    setTimeout(function() { console.log(i); }, 0);
  })(x);
}
```

From this example it should be clearer that the we are creating a new variable `i` in the declaration of the anonymous function. The variable of the for loop is irrelevant because `setTimeout` and its target function will have a brand new context on each iteration and in this new context `i` will have the expected value (1, 2, 3) on each iteration.


## The CoffeeScript Way

Trevor makes a great point on his post when he shows how much simpler the code to achieve the same result is in CoffeeScript:

```code
for i in [1..3]
  do (i) ->
    setTimeout (-> console.log i), 0
```

In this case `do(i) ->` is doing the dirty work of creating a new function and executing it. Gotta love the simplicity of CoffeeScript syntax!

However, I doubt many CoffeeScript newcomers will really understand how this work unless they understand how JavaScript works behind the scenes. I hope this blog post helps to clarify what it’s really going on behind the scenes.