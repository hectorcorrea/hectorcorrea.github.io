# Using layouts with EJS in Express 3.x
Over the last couple of months I've been meaning to update [my sample Express.js application](https://github.com/hectorcorrea/simple-blog) from Express 2.x to Express 3.x but the fact that support for layouts was removed from Express 3.x was holding me back.

In Express 2.x the framework itself supported the concept of layouts and both template engines, Jade and EJS, worked nicely with it. In Express 3.x the functionality has been removed from the framework and now the template engines need to be updated to provide this support themselves. 

This week I found that EJS has been updated to support [includes](https://github.com/visionmedia/ejs#includes) and that allowed me to emulate in Express 3.x the concept of layouts that I was getting with Express 2.x. This is probably the mother of all hacks, but it's simple and it works for my purposes so I am documenting it for others using Express 2.x and EJS that might be holding back on upgrading to Express 3.x just because of the lack of layouts in Express 3.x.


## EJS layouts in Express 2.x
In Express 2.x my *layout.ejs* file looked something like this:

```code
<!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1>Header</h1>
    <p>Welcome to <%= title %></p>
    <b><%- body %></b>
    <p>the footer</p>
  </body>
</html>
```

an a simple *index.ejs* file would look like this:

```code
  <p>the content of the page</p>
```


## EJS layouts in Express 3.x
To achieve a similar behavior in Express 3.x I ended up (1) splitting my layout.ejs file in two files (layoutTop.ejs and layoutBottom.ejs) and (2) updating my views to explicitly include the top and bottom layout files. The following example shows this:

The contents of *layoutTop.ejs** looks like this

```code
<!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1>Header</h1>
    <p>Welcome to <%= title %></p>
```

whereas the contents of *layoutBottom.ejs* looks like this

```code
    <p>the footer</p>
  </body>
</html>
```

and finally my *index.ejs* file now looks like this

```code
<% include layoutTop %>
<p>the content of the page</p>
<% include layoutBottom %>
```

Notice how the *index.ejs* explicitly requests the two layout files which is a bit annoying since in the previous version this was implicit but I think it's a small price to pay to keep the functionality working.

## A few final words...
Although this approach is not nearly as elegant as what Express 2.x and EJS provided, I liked it because its  simple enough that allowed me to upgrade to Express 3.x without having to switch template engines, download somebody else's fork of EJS, or invest a lot of time learning how to build a template engine to support this behavior.

I am sure a more elegant solution can be achieved by taking a closer look at both the EJS and Express 3.x source code but I will leave that for another day/blog post.