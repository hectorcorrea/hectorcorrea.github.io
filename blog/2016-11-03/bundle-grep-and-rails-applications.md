# Bundle, grep, and Rails Applications
When I first started programming in Ruby and Ruby on Rails one of the things that I struggled the most was finding *where a particular piece of code (a method, a class, a string) was defined*. This was particularly difficult when the code that I was looking for was defined in a gem and not in the code of my application.

A couple of years ago [Mike Giarlo](https://github.com/mjgiarlo)  showed me a great tip to search through the entire code of Rails application (your code and the code of your dependencies) by using `grep` and `bundle`. Today while working with somebody else new to Ruby I noticed that this tip needs to get broader attention since I think is incredibly helpful, particularly for newcomers.

Most Rails applications these days use `bundle` to manage dependencies and `bundle` provides an option to list the path to all the gems that your application is using. You can see this by running:

```terminal
$ bundle list --paths
```

For example, on a new brand new Rails application it will show you something along the lines of:

```code
/Users/xyz/.gem/ruby/2.3.1/gems/actioncable-5.0.0.1
/Users/xyz/.gem/ruby/2.3.1/gems/actionpack-5.0.0.1
/Users/xyz/.gem/ruby/2.3.1/gems/actionview-5.0.0.1
/Users/xyz/.gem/ruby/2.3.1/gems/activejob-5.0.0.1
/Users/xyz/.gem/ruby/2.3.1/gems/activemodel-5.0.0.1
/Users/xyz/.gem/ruby/2.3.1/gems/activerecord-5.0.0.1
plus a million other paths
```

By combining the output of `bundle list --paths` with the venerable Linux `grep` tool you can search through the entire code base for things that you have no idea where they have been defined. For example, let's say that you want to find the definition of the method `retry_job` that your Rails application is calling. You can issue the following command:

```terminal
$ grep -r "retry_job" $(bundle list --paths) .
```

and review the output which would be something like this:

```code
.../2.3.1/gems/activejob-5.0.0.1/CHANGELOG.md:     retry_job(wait: 10)
.../2.3.1/gems/activejob-5.0.0.1/lib/active_job/core.rb:  # retry_job(wait: 10)
.../2.3.1/gems/activejob-5.0.0.1/lib/active_job/enqueuing.rb: # retry_job queue: :low_priority
.../2.3.1/gems/activejob-5.0.0.1/lib/active_job/enqueuing.rb: def retry_job(options={})
```

The result shows that this method is defined in the `activejob` gem, in fact the fourth file that grep returned points directly to the line where the method is defined, notice the `def retry_job()` on the last match.

To be fair, most of the Rails code and its API are very well documented and a simple  Google search tends to give good results for things defined in Rails. However, not all gems are as well documented as Rails, and Google does not always do a good job with some of the less popular gems. I use the `bundle grep` approach that I describe in this blog post when I am trying to figure out which gem defined  a particular function or class, or when I am depending on gems that are not nearly as well documented as Rails itself.

One thing to notice is the dot at the end of the `grep` command.  This is important since that instructs `grep` to search *also* on the current folder. If you leave this out `grep` will only search on the gems defined in your application, but not in your own application.

I use this method so much that I have it defined as [a function in my bash initialization script](https://gist.github.com/hectorcorrea/fd56edea3333bec47829#file-bashrc-sh-L39) so that I can always type `bgrep text-to-search`.  I know there are [other developers](https://gist.github.com/grosscol/494e972e1b9dc1dfac18430f4d631593) that use the same approach with `ag` (the Silver Searcher) instead of `grep` but the idea is the same.

**Update 12/22/2016:** Today I ran across [this great blog post](https://tenderlovemaking.com/2016/02/05/i-am-a-puts-debuggerer.html) by Aaron Patterson with a bunch of tips to debug Ruby applications that you probably want to check out too.

**Update 2/10/2021:** Updated to use the new bundle syntax `bundle list --paths` instead of `bundle show --paths`
