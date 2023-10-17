# Static Generated Site
*Posted on 2023-10-17*

Earlier this month I migrated my personal site at [https://hectorcorrea.com](https://hectorcorrea.com) to be hosted as a statically generated set of HTML pages hosted on GitHub pages. This post elaborates on why and how I did it.

Recently I have been interested in how much we can achieve on the web with just the basics (HTML + CSS + JavaScript) and in keeping with the tradition of trying things on my personal site I decided to convert this site to a static generated set of pages and see how well this idea works in practice.

## The previous version

The previous version of my site was served via a small Go program running on a Linux machine hosted in Digital Ocean. In 2017 I [blogged about my setup](https://hectorcorrea.com/blog/2017-01-18/migrating-my-site-from-node-js-to-go) and I am really impressed how low key and low maintenance that approach was for 6 years.

The initial version of my Go program stored its content (web pages and blog entries) on a MySQL database. At one point in 2022 I changed it to store the content as Markdown files and render those files as HTML at runtime. I really liked the idea of having the content as plain text files for a site like my blog. I could easily edit the content on any editor on my machine and navigating through all the entries was also straightforward.

## Going static

Since the content of my blog was already stored as text files, switching to a static generated version was not a big leap. I thought about using something like Hugo or Jekyll to parse my Markdown files and generate the static content. But, since I already have Go code to convert the Markdown to HTML I decided to write a tool to do the job for me.

So that's what I did. I wrote a little Go program that parses Markdown files and generates the HTML for each of them. Given I use my personal site mostly to blog, this little Go program has logic to generate the specific blog pages (like an index that list all blog entries grouped by year) and an RSS feed so that the content of the blog can be consumed via RSS readers.

The program that I wrote is called **fixie** and it's [available on GitHub](https://github.com/hectorcorrea/fixie).

## Hosting the static site

Initially, when I first decided to create a static generated version of my site, I was intending on hosting that version on the same Linux machine that I already have at Digital Ocean and just serve the HTML pages through Apache. However, as I was converting the site it dawned on me that I could also host it on [GitHub pages](https://pages.github.com/) which is what all the cool kids are doing these days.

I have never hosted a site on GitHub pages so this was new territory to me. But the documentation is fantastic and it didn't take me long to push my content to a GitHub repository that is served via GitHub pages directly.

GitHub pages also has great instructions on how to [configure DNS](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site) so that my original URL `https://hectocorrea.com` can be served by the content in GitHub pages. This was a bit tricky mostly because DNS is tricky and I am not super comfortable with it. But all in all the process as smooth as it can be.

## Legacy pages and redirects

This is not the first time that I rewrite the code to host my blog, and over the years I have changed the structure of the URLs for the blog entries. But every time I change the URLs I have tried to make sure legacy links are redirected properly to the new URLs. This is something that I have done relatively well over the years and I really didn't want to lose that functionality with this new version.

When the site was using a relational database as the backend I used to include the record id on the URL (say `/blog/hello-world/10`). When I switched to using text files as the backend I replaced the record id with a date and a sequence number that matched the file on disk (say `/blog/hello-world/2012-09-02-00028`).

To keep with the (bad) tradition of changing URLs, in this version of the site I decided to change the URLs again and move the date closer to the front of the URL and drop the sequence number so now the URL looks like this `/blog/2012-09-02/hello-world`

Handling legacy URLs when the site was served by a Go program was easy because I could inject code to analyze URLs and handle legacy URLs differently, but when I statically generate all the pages ahead of time I have to come up with a solution to also generate pages to handle the legacy URL pages.

The program  that I wrote to generate the static pages (`fixie`) has logic that accounts for the legacy URLs and generates stub files. For example, it generates two files (`/blog/hello-world/10.html` and `/blog/hello-world/2012-09-02-00028.html`) with the HTML content to tell the browser to redirect the user to the new URL:

```
<head>
   <meta http-equiv="Refresh" content="0; URL=/blog/2012-09-02/hello-world" />
</head>
```

This way if somebody goes to `/blog/hello-world/10` a file will be found and that file will tell the browser to send the user to the new URL: `/blog/2012-09-02/hello-world`. This is known as [HTML redirection](<https://developer.mozilla.org/en-US/docs/Web/HTTP/Redirections#html_redirections>).

A disadvantage of this approach is that there are lots of extra files on my GitHub repo that are just stub files (i.e. they contain no real content, only the HTML redirect). But, at least the links are redirected properly so I guess I'll have to live with this.

## Page not found
GitHub pages automatically loads a file called `404.html` everytime somebody visits a link that does not exist in the GitHub site and it renders it with the proper HTTP response status `404` which is nice. You can customize the content of this page to tell your users that something was not found using whatever layout your site uses.

I think I am handling most of the legacy links that exist out there on the web for my blog. But unfortunately I have no way of knowing since GitHub does not report how often this page is rendered and for what URLs.

As of today there are still a few broken links to images and files available for download in some of the blog entries. I'll fix those in the next few days.

## Source code, err, content

The source content of my site can be found at [https://github.com/hectorcorrea/hectorcorrea.github.io](https://github.com/hectorcorrea/hectorcorrea.github.io). This repo includes the source Markdown files with the content, the HTML generated pages, and HTML stub files for redirection.

The source code for `fixie` is also [available on GitHub](https://github.com/hectorcorrea/fixie).
