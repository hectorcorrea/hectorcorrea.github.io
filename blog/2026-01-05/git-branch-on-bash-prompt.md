# Displaying the Git branch on the Bash prompt
*Posted on 2026-01-05*

A few days ago I updated my Bash initialization script to display the current Git branch on the prompt.

As it turns out there several ways to achieve this and after trying a few of them I settled on creating a very small Bash function to return the current branch if I am on on a Git project (empty otherwise) and stick this value to the `PS1` variable which is what Bash uses to determine what is included in the prompt.


## The short story
Below is the code that I added to my `.bash_profile` to achieve this:

```
# Returns the current git branch if we are in a Git repo, empty otherwise.
function git-branch {
  git_branch=`git branch --show-current 2>/dev/null`
  if [ -z "$git_branch" ]; then
    echo ""
  else
    echo "$git_branch "
  fi
}

# Include the branch name in the prompt
PS1='$(git-branch)\w \$ '
```

Once I added this code to my `.bash_profile` the Bash prompt now includes the current branch name of the project that I am on, for example:

```
main ~/src/marcli $
```

or just the path if I am on a directory that is not a Git repository:

```
~/Downloads $
```

Since I put the changes in my `.bash_profile` I had to close and re-open my Terminal for the changes to take effect.


## Getting the branch name
Git provides a convenient command to get the branch name: `git branch --show-current` which is what I use in my example.

NOTE: I think previous versions of Git did not support the `--show-current` parameter and there are several examples on the web (like [this](https://www.glukhov.org/post/2025/12/adding-git-repo-details-to-bash-prompt/) or the one in [StackOverflow](https://stackoverflow.com/a/6086978/446681)) that show how to extract the current branch through `sed`, but this is not needed on versions of Git that support the `--show-current` parameter.

The `git branch --show-current` returns the branch name *if you are* in a Git project but it returns an error if you are not:

```
$ cd ~/Downloads
$ git branch --show-current
fatal: not a git repository (or any of the parent directories): .git
```

In my example I ignore the error by redirecting it to `/dev/null` with the `2>/dev/null` syntax. This is necessary so that the error is not displayed on the Terminal every time I switch to a non-git directory.


## Using the official git-prompt code
Git itself includes a script that provides the same (and much more) functionality as my tiny script and you can also use this to get the same behavior.

The script is called `git-prompt.sh` and on my Mac I found it under `/Library/Developer/CommandLineTools/usr/share/git-core/git-prompt.sh`. I understand [it can be in several different locations](https://www.glukhov.org/post/2025/12/adding-git-repo-details-to-bash-prompt/) depending on how you installed Git or your operating system.

The script is rather comprehensive (672 lines long in my Git 2.50.1 installation) since it has code that allows is to work with different operating systems, shells, and handles much more information than just the current branch.

If you use this script then just need to source it and update your `PS1` variable to use it:

```
source /Library/Developer/CommandLineTools/usr/share/git-core/git-prompt.sh
PS1='[\u@\h \W$(__git_ps1 " (%s)")]\$ '
```

I didn't quite like the way it behaved for me, it felt slugish, so I decided against it and used my tiny function instead. But the `git-prompt.sh` script has wealth of information and it's worth checking out if you are updating your prompt with the Git branch information, you can also [view it directly in GitHub](https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh).