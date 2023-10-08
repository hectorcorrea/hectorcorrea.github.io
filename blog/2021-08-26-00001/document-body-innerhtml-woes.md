# document body innerHTML woes
Today I ran into an issue setting the value of `document.body.innerHTML` that caught me by surprise. I have always known that I can set an HTML fragment to `document.body.innerHTML` and the rendering engine (the browser usually) does the right thing and renders it correctly. You can even pass a *malformed HTML* and the rendering engine usually makes pretty good guesses and parses it correctly.

But today I used a fragment that caused the rendering engine to parse the HTML in a way that had me confused for a while. What was worse, is that the HTML that I used was properly formed, but turns out it was incomplete in a way that the rendering engine parsed it all wrong.


## A good fragment
Below is an example of a good HTML fragment that the browser knows how to parse:

```
document.body.innerHTML = "<p>hello <b>world</b></p>"
console.log(document.body.innerHTML)

<p>hello <b>world</b></p>
```

Notice that there is no `<html>`, `<head>`, or `<body>` HTML tag and yet the browser parses this correctly. You can test this by opening a *new browser window*, then *inspect* the page, and on the *console* type the line above.

In the examples in this post I print via `console.log(document.body.innerHTML)` the value assigned rather than relying on the value that the browser displays immediately after the assignment. This is important because the value assigned could be different from the value given since the browser parses it before assigning it. This is what the [MDN documentation](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML) says on the subject: 

> Setting the value of innerHTML removes all of the element's descendants and replaces them with nodes constructed by parsing the HTML given in the string htmlString.


## A malformed fragment
Now let's see what happens if I use a malformed HTML fragment. In the example below I replaced the `<b>` HTML tag with `<span>` but I left the closing `</b>` tag and did not include a closing `</span>` tag.

```
document.body.innerHTML = "<p>hello <span>world</b></p>"
```

In this case the browser is pretty smart and will parse this correctly and if you were to print the new value to the console you will see something similar to this:

```
document.body.innerHTML = "<p>hello <span>world</b></p>"
console.log(document.body.innerHTML)

<p>hello <span>world</span></p>
``` 

Notice how the value that was parsed and eventually assigned to `document.body.innerHTML` includes the proper `<span>` and `</span>` tags and ditches the extra closing `</b>` tag. The browser had to guess and it did a pretty good job. You could argue that it could have replaced the opening `<span>` tag and with `<b>` but the browser had to make a choice and what it did is reasonable. 


## A problematic fragment
But today, while dealing with an HTML fragment that was part of a table I ran into a strange issue that turns out has a perfectly valid explanation. 

Below is a simplified version of fragment that I had: a table with just one row and two columns. Notice how there is not `<table>` HTML tag, instead there is `<tbody>`, and turns out that was the real issue behind my problem:

```
document.body.innerHTML = "<tbody><tr><td><span>row 1, colum 1</span></td><td><span>row 1, column 2</span><tr></tbody>"
console.log(document.body.innerHTML)

<span>row 1, colum 1</span><span>row 1, column 2</span>
```

Because there was no `<table>` HTML tag the rendering engine did not know what to do with the `<tr>` and `<td>` tags and discarded them and just preserved the `<span>` tags. This resulted in a very different HTML document than the one that I intended, and because in my case I needed this HTML fragment for a JavaScript test against the DOM this was a big problem. Plus in my case the HTML table was much bigger than this example and that made the problem harder to spot since the resulting HTML looks OK at first glance.

If my HTML fragment had started with `<table>` instead of `<tbody>` the rendering engine would have parsed the fragment correctly and preserved the `<tr>` and `<td>` tags, heck it even added the missing `<tbody>` tag if I don't put it:

```
document.body.innerHTML = "<table><tr><td><span>row 1, colum 1</span></td><td><span>row 1, column 2</span><tr></table>"
console.log(document.body.innerHTML)

<table><tbody><tr><td><span>row 1, colum 1</span></td><td><span>row 1, column 2</span></td></tr><tr></tr></tbody></table>
```

The moral of the story is to make sure that the HTML fragment that you assign to `document.body.innerHTML` is parsed as you would expect.