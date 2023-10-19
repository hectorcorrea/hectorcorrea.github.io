# fixie
*Posted on 2023-10-18*

A few weeks ago I wrote a little program called (`fixie`) to help me migrate my personal website to be served via statically generated pages.

`Fixie` is similar to other static site generators like [Hugo](https://gohugo.io/) or [Jekyll](https://jekyllrb.com/): you give it a bunch of Markdown files and it produces the HTML pages with the content on those Markdown files. But `fixie` has zero customization options, well not zero, but very few customization options - [hence the name](https://en.wikipedia.org/wiki/Fixed-gear_bicycle).

## Basics

Let's say that you have two Markdown files `about.md` and `index.md` in your current folder. If you run `fixie` on that folder it will create `about.html` and `index.html` with the HTML version of the Markdown files.

`Fixie` processes all files in the current directory and anything underneath. So if there is a Markdown file under `./help/how-to.md` that file will also be processed and the corresponding HTML will be generated `./help/how-to.html`

At its core, that's all `fixie` does, but you can customize it a little bit.

## Styling the pages

If there is a `layout.html` on the current folder, `fixie` will use the content of this file as the as the base creating the HTML version of `about.md` and `contact.md`.

The `layout.html` file is a normal HTML file that you create with the skeleton (header, styles, footer, JavaScript) that will be used for all the generated HTML pages. This file is expected to include a token `{{CONTENT}}` somewhere on the HTML body (say inside a `div`) and that token will be replaced with the content of each of the Markdown files as the HTML version is generated.

For a given file, say `about.md`, the process is as follows:

```
1. read about.md
2. read layout.html
3. replace {{CONTENT}} in layout.html with the HTML for about.md
4. save it as about.html
```

## Blog entries

I created `fixie` to help me migrate my personal site, which is a really a blog, as a result `fixie` has built-in provisions for a basic blog web site.

Any Markdown file that is under the `./blog` folder is considered a blog entry. `Fixie` processes these files as any other Markdown file: if there is a file called `./blog/2021-12-20/static-sites.md` it will create the corresponding HTML file `./blog/2021-12-20/static-sites.html` using the `layout.html` file.

But there are several other goodies that happen to these blog entries:

* If the filename or the path to the file has a date in the form `YYYY-MM-DD` (e.g. 2021-12-20 in the example above) that date is considered the date the blog was posted.
* If the first line of the Markdown file starts with a header line (e.g. `# Static sites are cool`) that line is considered the title of the blog entry. Otherwise the filename is used as the title.
* An extra HTML file `./blog/index.html` is generated with all the blog entries listed, grouped by year, and with the most recent entry first. This HTML uses the same `layout.html` as the rest of the site, except that `{{CONTENT}}` is automatically replaced with the list of blog entries.
* An extra XML file `./blog/rss.xml` is generated with the RSS feed for the blog entries.

You need to manually reference these two files (`./blog/index.html` and `./blog/rss.xml`) if you want to link to them from your site. For example, you can add links in `layout.html` or somewhere in one of you other Markdown files (e.g. `about.md`)

`Fixie` does a few other extra things if there is an XML file next to each Markdown file under the `./blog` folder. This is because the data from my previous web site kept metadata for blog entries in separate XML files. You do not need to create these separate XML files, but if you go poking around the source code you'll notice code that uses these files - just ignore it.

## Local web server

`Fixie` produces HTML files and sometimes it's nice to preview the HTML site before you push it to an actual web server, particularly if you are tweaking the layout.

You can run `fixie -server` to run a local web server and preview the content of your web site. By default the site is available on `http://localhost:9001`. Keep in mind that this local server is just for you to preview your files, not to actually host your site.

This local server will not automatically detect changes to the Markdown files and regenerate them (i.e. it does not support hot reload). If you make changes to the Markdown files you need to stop the server and rerun it so that it regenerates the HTML.

## Getting started

You can download the `fixie` executable from [https://github.com/hectorcorrea/fixie/releases](https://github.com/hectorcorrea/fixie/releases) or via the command line with cURL:

```
$ curl -LO https://github.com/hectorcorrea/fixie/releases/latest/download/fixie
$ chmod u+x fixie
```

Once `fixie` is on your machine, make sure you have some Markdown files to process (and optionally a `layout.html`). If you don't have any sample files you can download the ones in the [sample folder](https://github.com/hectorcorrea/fixie/tree/main/sample) in the GitHub repo to get started.

```
$ ls
about.md
index.md
layout.html
```

Now let's run `fixie` to process these Markdown files:

```
$ ./fixie
fixie - a one gear static site generator

Using layout file: ./layout.html
Processing .md files...
  about.md
  index.md
No blog entries (./blog/) were found
```

Notice how it reports that it is using our `layout.html` file and that it processed `about.md` and `index.md` . If you list your files again you should notice now the newly generated `about.html` and `index.html` files.

```
$ ls
about.md
about.html # Generated by fixie
index.md
index.html # Generated by fixie
layout.html
```

At this point you can also preview your site by running `./fixie -server` and pointing your browser to `http://localhost:9001`

You might have noticed that when we ran `./fixie` it reported that "No blog entries (./blog/) were found". This is because in our example we don't have a `./blog` folder with Markdown files, but if you create one you should see how those files are also processed and recognized as blog entries.

## Source code

The source code is in Go, if you are interested in the code you can find it at [https://github.com/hectorcorrea/fixie](https://github.com/hectorcorrea/fixie).

I am using `fixie` to produce the HTML for my personal site, [check out this blog entry](https://hectorcorrea.com/blog/2023-10-17/static-generated-site) for more information on why I did it and how I am using it to host my site at GitHub pages.
