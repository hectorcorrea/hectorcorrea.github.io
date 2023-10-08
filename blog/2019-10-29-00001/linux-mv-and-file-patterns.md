# Linux mv and file patterns
Last week I ran into an interesting issue renaming a bunch of files in my machine. The command I ran was:

```
$ mv &#42;.jpeg &#42;.jpg
```

I was expecting this command to rename all `.jpeg` files to `.jpg` but instead the command returned

```
$ mv &#42;.jpeg &#42;.jpg
usage: mv [-f | -i | -n] [-v] source target
       mv [-f | -i | -n] [-v] source ... directory
```

At first I thought I had a typo in my command but the reality is that this is not a valid `mv` command. 

The culprit is that my shell (Bash) is expanding `&#42;.jpeg` and `&#42;.jpg` *before* executing the `mv` command and understandably `mv` refuses to continue. To illustrate this let's assume there are two `.jpeg` files and no `.jpg` files on my machine, when I execute my original command what Bash passes to `mv` is something like this:

```
$ mv file1.jpeg file2.jpeg &#42;.jpg
```

Notice that Bash expanded `&#42;.jpeg` into the two individual files and  `&#42;.jpg` stayed as-is since there is nothing to expand it into. Certainly not what I was intending to do. A few searches on the web showed many people having this same problem, here is [one example](https://unix.stackexchange.com/questions/181141/rename-multiple-files-with-mv-to-change-the-extension).

Working around this issue was easy enough. I wrote a quick shell script to do the job:

```
for FILE in &#96;ls -1 &#42;.jpeg&#96;
do
    BASENAME=&#96;basename $FILE .jpeg&#96;
    TARGET=$BASENAME".jpg"
    mv $FILE $TARGET
done
```

I posted about this issue on Twitter and got several suggestions on how to make this script better. The solution provided by [John Borwick](https://twitter.com/borwick/status/1188697953768050689) in particular is very neat:

```
for FILE in &#42;.jpeg
do
    TARGET="${FILE%.jpeg}.jpg"
    mv "$FILE" "$TARGET"
done
```

A few other people suggested that I use either `rename` or `mmv` to do what I was trying to do. Both of these utilities can be installed easily on Mac or Linux systems. 

Using [mmv](https://www.cgarbs.de/blog/archives/430-mmv-mass-rename-of-files-and-more!.html) the syntax to rename the files is:

```
$ mmv -n "&#42;.jpeg" "#1.jpg"
```

Using [rename](https://www.putorius.net/rename-command-linux.html) the syntax is:

```
$ rename -xa .jpg &#42;.jpeg
```

One of the things that dawned on me while reading the blog post for [mmv](https://www.cgarbs.de/blog/archives/430-mmv-mass-rename-of-files-and-more!.html) and the local man pages for `rename (1)` was that my original command would have worked in DOS, and I was a DOS/Windows user a long time ago. The `mmv` blog post even says 

> Under good old DOS you could rename multiple files by saying 
> REN A&#42;.&#42; B&#42;.&#42;

but the reason this worked is because DOS had pretty simple shell that didn't support Unix style pathname pattern expansion (glob). The man page for `rename (1)` on my system even has the following note:

> ...
> -g, --glob
>    Glob filename arguments. This is useful if you're using a braindead shell such as cmd.exe which won't expand wildcards on behalf of the user.
> ...

So that explains why I was so puzzled. I probably did rename files with a command "like this" before, but it must have been 10 years or so ago and in a different operating system :)

**Note:** Beware that `rename` is a native system function in Linux systems and `man rename` might give you information about the function `rename (2)` rather than the program `rename (1)` if the program is not installed on your machine. See [also](https://unix.stackexchange.com/questions/3586/what-do-the-numbers-in-a-man-page-mean).