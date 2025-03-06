# Installing Ruby with ruby-install and chruby
*Posted on 2018-09-22, updated on 2024-02-07*

When I started writing code on Ruby I learned there were many ways to download and install Ruby on a Mac. Initially I was comfortable with the version of Ruby that comes installed on OS X by default or using the installer available at [ruby-lang.org](https://www.ruby-lang.org/en/downloads/)  to install the latest version. As my needs evolved I needed to have more than one version of Ruby installed on my machine to work on different projects that use specific versions of Ruby.

There are many tools to install and manage multiple versions of Ruby on a Mac. Among the options that I tried were [RVM](https://rvm.io/), [rbenv](https://github.com/rbenv/rbenv), and [chruby](https://github.com/postmodern/chruby). In the end I found the combination of `chruby` and `ruby-install` the best for my needs, in large due to their minimalist approach.

Below are the instructions that I follow on a brand new Mac to install specific versions of Ruby and switch among them. First install `ruby-install` and `chruby` via [Homebrew](https://brew.sh/):

```
$ brew install ruby-install
$ brew install chruby
```

Then I use `ruby-install` to install a particular version of Ruby, for example to install version 2.3.5 I use:

```
$ ruby-install ruby 2.3.5
```

You can also use `ruby-install --latest` to install the latest version of Ruby available.

`chruby` provides a script that you can add to your Bash init script to let you change to a specific version of Ruby installed on your machine and configure the environment to point to the correct locations for that Ruby version by setting your PATH, GEM_HOME, and GEM_PATH environment variables. These are the lines that I add to my `.bashrc` file to automatically select Ruby 2.3.5 on my machine as the default Ruby.

```
source /usr/local/opt/chruby/share/chruby/chruby.sh
chruby 2.3.5
```

`chruby` also provides another script (`auto.sh`) to automatically switch the version of Ruby as you `cd` into a specific project. I do **not** use this script, I prefer to manually issue a `chruby` command when I want to switch versions.

And that's pretty much all I need to setup my Ruby environment on a Mac. Pretty simple and non-intrusive.

The rest of this post are a few notes on things that I've learned about `ruby-install` and `chruby` that were not obvious to me when I first started using them.


## ruby-install oddities

One thing that still confuses me with `ruby-install` is that it does not keep a list of newer versions of Ruby available to download and install. For example even though Ruby 2.5.1. is now available at the ruby-lang.org web site this is what `ruby-install` shows on my machine:

```
~ $ ruby-install --version
ruby-install: 0.7.0

~ $ ruby-install
Stable ruby versions:
  ruby:
    2.1.9
    2.2.7
    2.3.4
    2.4.1
  jruby:
   # more stuff
```

Notice that there is no mention whatsoever of version 2.5.1. You **can request to install a newer version of Ruby** if you know there is one available, for example I can issue `ruby-install ruby 2.5.1` and it will install this version.

```
$ ruby-install ruby 2.5.1
*** Unknown ruby version 2.5.1. Proceeding anyways ...
>>> Installing ruby 2.5.1 into /Users/hector/.rubies/ruby-2.5.1 ...
>>> Installing dependencies for ruby 2.5.1 ...
# continues with the installation...
>>> Successfully installed ruby 2.5.1 into /Users/hector/.rubies/ruby-2.5.1
```

Even more puzzling is that *even after downloading and installing Ruby 2.5.1 on my machine* ruby-install will not show that it is available when I run `ruby-install` without arguments.

The last gotcha that I'll point out is that you must indicate "ruby" when you run ruby-install, for example notice the error that I get when I run `ruby-install 2.5.1` rather than `ruby-install **ruby** 2.5.1` (Update: it seems that with newer versions of `ruby-install` this is not longer an issue, I recently tried with version `0.9.3` and didn't have this issue)

```
$ ruby-install 2.5.1
!!! Unknown ruby: 2.5.1
```

Despite these peculiarities I find `ruby-install` a great tool to get my environment ready since I usually know what version of Ruby I am after.


## Common errors with Ruby 3.x
Since Ruby 3.x I have experienced a new set of issues when installing Ruby. The errors are not related to `ruby-install` per-se since they also happen if you try to [install Ruby from source](https://www.ruby-lang.org/en/news/2024/01/18/ruby-3-2-3-released/).

The first issue has to do with Open SSL. If `ruby-install` throws an error related to Open SSL the first thing that I will suggest is to use the `--with-openssl-dir` flag to tell it what Open SSL library to use. For example:

```
$ ruby-install ruby 3.3 -- --with-openssl-dir=$(brew --prefix openssl@3)
```

Notice that since December of 2024 OpenSSL 1 has been deprecated and you must use OpenSSL 3.

The other issue that I've seen recently is "error: use of undeclared identifier 'RUBY_FUNCTION_NAME_STRING'". This [blog post by Franklin Yu](https://dev.to/franklinyu/error-of-rubyfunctionnamestring-when-compiling-ruby-32b8) gives a great explanation of the problem and notes that many people have reported that the culprit in this case is the version of the Xcode command line tools installed on your Mac. To address this you can [remove your Xcode command line tools and install them again](https://github.com/rbenv/ruby-build/discussions/1938#discussioncomment-2213912):

```
$ sudo rm -rf /Library/Developer/CommandLineTools
$ sudo xcode-select --install
```

The `xcode-select --install` command will display "xcode-select: note: install requested for command line developer tools" as the output on the terminal but *it will also display a dialog asking you to confirm that you want to install the Xcode tools*, make sure you accept that prompt.


## chruby notes

Something that surprised me when I started using `chruby` was that when you `brew install chruby` you don't get a `chruby` executable per-se, if you issue `which chruby` the OS will report nothing. That's because `chruby` is actually a Bash function that is defined in the `/usr/local/opt/chruby/share/chruby/chruby.sh` script. Once you `source` this script the OS will report that is indeed a function. For example:

```
$ type chruby
-bash: type: chruby: not found

$ source source /usr/local/opt/chruby/share/chruby/chruby.sh
$ type chruby
chruby is a function
chruby ()
{
   # code for the function goes here
}
```

Another quirky thing with `chruby` and `ruby-install` is that after installing a new version of Ruby you must reload the `/usr/local/opt/chruby/share/chruby/chruby.sh` shell script for the new version to be available to chruby. In practice I typically just close my terminal window and open a new one to force this to happen since the chruby script is loaded by my Bash init script.




