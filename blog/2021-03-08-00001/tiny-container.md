A few weeks ago I read the book [Linux Containers and Virtualization - A Kernel Perspective](https://www.apress.com/gp/book/9781484262825) by Shashank Mohan Jain and I was fascinated by the example that he provides on how to build your own container from scratch in Go. In this blog post I replicate a part of the code that Mr. Jain presents but I tweaked it to run its own shell (rather than having to download a separate set of Linux libraries from the web). My version of the code only supports the functionality to show isolation of the file system whereas the original code in the book shows how to provide network and memory isolation.

The code examples that I created are available at [https://github.com/hectorcorrea/tiny-container](https://github.com/hectorcorrea/tiny-container). File `tinyContainer.go` is a copy of the code provided by Shashank Mohan Jain but tweaked as mentioned above. File `tinyShell.go` is a program that I created to emulate a few commands that a typical Linux shell provides (like `cat`, `cd`, `env`, `ls`, and `hostname`) so that we can test the isolation of the file system that `tinyContainer.go` provides.

I should point out that I am a newbie when it comes to Linux kernel stuff and system calls and this topic is way out of my league. Please feel free to reach out if I got some of the concepts wrong or they could use a better explanation.

## What are containers anyway
Although I have heard that *containers* were a feature of Linux, my only exposure to them had been through third-party tools like Docker. Mr. Jain's book provides a good introduction on how containers are supported by the Linux kernel itself and many of the features available to configure them.

One of the things that Mr. Jain points out is that containers, like virtual machines, are a virtualization technique. But whereas virtual machines virtualize a complete instruction set architecture (i.e. the whole machine) a container is a much lighter technique that creates isolated sections within the operating system. As Mr. Jain says:

> [container-based virtualization] doesn’t abstract the hardware but uses techniques within the Linux kernel to isolate access paths for different resources. It carves out a logical boundary within the same operating system. As an example, we get a separate root file system, a separate process tree, a separate network subsystem, and so on.
 
According to Mr. Jain Linux containers are made of three Linux kernel primitives:

* *Linux namespaces* which provide logical isolation and controls visibility within the kernel, this is what allows Linux to sandbox the programs running inside a container. There are many types of namespaces and each of them controls different parts of the operating system like process IDs (PID), mounts, and network access. 
* *Control Groups* (aka cgroups) are used to control CPU and disk utilization within the container.
* *Layered file systems* which allow a degree of sharing of files between the host operating system and the container.

## Creating a container from scratch (with Go)
There are two code examples that are part of this blog post: `tinyContainer.go` and `tinyShell.go`. 

The bulk of the functionality lives on `tinyContainer.go`, this is the program that creates the container and defines its properties. `tinyShell.go` is a little program that emulates a shell so that we can interact with the container and run commands inside it once it has been created.

If you want to execute these programs you can follow these steps to download the code, compile it, and execute it. 

```terminal
$ git clone https://github.com/hectorcorrea/tiny-container.git
$ cd tiny-container
$ GOOS=linux go build -o tc tinyContainer.go
$ GOOS=linux go build -o ts tinyShell.go

$ ./tc -root=/root/tiny-container -shell=./ts
Tiny shell started
ts: _
```

Notice that this code will only execute on Linux machines since we are making use of Linux system calls.

### tinyContainer.go
The main goal of [tinyContainer.go](https://github.com/hectorcorrea/tiny-container/blob/main/tinyContainer.go) is to create the container and configure it, however the process to do this is a bit tricky so let's see how this is done:

* When we first run this program it creates a container
* Then the program re-launches itself but now *within the container* so now we are running inside the container
* Once running inside the container we configure a few things, like a new hostname and a new root directory
* At the end the program launches a shell so that we can interact with the container and see that it is indeed sandboxed.

The [main()](https://github.com/hectorcorrea/tiny-container/blob/main/tinyContainer.go#L47) function in `tinyContainer.go` takes care of these steps and it looks more or less like this:

```
func main() {
  if xaction == "create" {
    // This is the default action: create the wrapper for the container
    createContainer(root, shell) 
    os.Exit(0)
  }

  if xaction == "launch-shell" {
    // This is used internally to launch the shell inside
    // the container. Therefore this command must be executed
    // AFTER the container has been created.
    runShell(root, shell)
    os.Exit(0)
  }
}
```

When *we* run this program it will run the `createContainer()` function. This function launches a new program but this program will be launched in a new namespace and therefore it will be sandboxed. The way to do this in Go is via a typical call `exec.Command()` but the trick is in passing a few `CloneFlags` to tell the operating system that we want to sandbox it. Notice the `syscall.CLONE_NEWNS | syscall.CLONE_NEWUTS ...` in the code below to create new namespaces for the new program.

```
func createContainer(root string, shell string) {

  args := []string{"-root=" + root, "-shell=" + shell, "-x-action=launch-shell"}
  cmd := exec.Command("/proc/self/exe", args...)
  cmd.Stdin = os.Stdin
  cmd.Stdout = os.Stdout
  cmd.Stderr = os.Stderr

  // These flags are what instruct Linux to create a new container
  // (notice NEWNS, NEWUTS, ...) as it runs the command.
  var flags uintptr
  flags = syscall.CLONE_NEWNS | syscall.CLONE_NEWUTS |
    syscall.CLONE_NEWIPC | syscall.CLONE_NEWPID |
    syscall.CLONE_NEWNET | syscall.CLONE_NEWUSER

  cmd.SysProcAttr = &syscall.SysProcAttr{
    Cloneflags: flags,
    UidMappings: ...
    GidMappings: ...
  }

  if err := cmd.Run(); err != nil {
    fmt.Printf("Error running the /proc/self/exe container - %s\n", err)
    os.Exit(1)
  }
}
```
The call to run `/proc/self/exe` is just a shortcut to relaunch the program that is currently executing. 

Notice that one of the arguments that we pass when we relaunch the program is `-x-action=launch-shell` which will be detected in the `main()` function and will cause the program to call the `runShell()` function to configure a few settings now that we are running inside the container and will then start a shell session so that we can interact with the container.

The code for the `runShell()` function is relatively simple and it's listed below. The first thing we do here is change the name of the host to "tinyhost", notice that since we are now running inside our container this change will only affect the container's hostname, not the name of the Linux machine where we are running our program. Then we make a call to `pivotRoot()` to switch what folder is now considered the root folder (i.e. where will we be when we issue `cd /`). The code inside [pivotRoot()](https://github.com/hectorcorrea/tiny-container/blob/main/tinyContainer.go#L149) makes use of Linux's built-in [pivot_root](https://www.man7.org/linux/man-pages/man8/pivot_root.8.html) feature via system calls and I just took the code as-is from Mr. Jain's book. The last thing we do is launch our shell program so that we, as users, can now interact with the container. Notice that we also set an environment variable `tiny_demo` that will be available inside the container but not outside of it.

```
func runShell(root string, shell string) {

  // Set the hostname
  err := syscall.Sethostname([]byte("tinyhost"))
  
  // Pivot to our new root folder
  err = pivotRoot(root)
  
  // Launch the new shell session
  cmd := exec.Command(shell)
  cmd.Env = []string{"tiny_demo=something tiny"}
  cmd.Stdin = os.Stdin
  cmd.Stdout = os.Stdout
  cmd.Stderr = os.Stderr
  err = cmd.Run()
  if err != nil {
  	fmt.Printf("Error running the shell %s - %s\n", shell, err)
  	os.Exit(1)
  }
  
}
```

### tinyShell.go
It turns out that running a shell now that we are inside the container and that *we have switched the root folder* is not as easy as it sounds since now our program does not have access to all the utilities available in the typical `/usr/bin` folder of a Linux installation.

You might be tempted to say why not just copy the `bash` executable from the host `/usr/bin` to whatever folder we designate as our new root folder? This will work, except that Linux binaries have dependencies and you will need to also copy those dependencies and they are not always easy to track down.

In this post we are going to look at two ways to run a shell inside the container. One is to built a fake shell with Go and copy that program to the folder that we will designate as root. This is relatively easy to do because Go produces statically compiled binaries, so you can just copy one file and be done with it, no dependencies to track down.

The code in [tinyShell.go](https://github.com/hectorcorrea/tiny-container/blob/main/tinyShell.go) in the GitHub repo implements this fake shell. This program is a basic [read-eval-print-loop](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) (REPL) and supports a very limited set of commands: `cd`, `env`, `hostname`, `ls`, and `pwd`, just  enough to see that our container is indeed sandboxed.

Before we create our container and run a shell inside of it let's look at some of the values that our Linux host reports in a typical shell session:

```terminal
$ pwd
/root/tiny-container

$ env
SHELL=/bin/bash
PWD=/root/tiny-container
LOGNAME=root
XDG_SESSION_TYPE=tty
TERM=xterm-256color
HOME=/root
...

$ hostname
ubuntu-20-test
```

Assuming that you compiled `tinyContainer.go` as `tc` and `tinyShell.go` as `ts` as indicated at the beginning of this post, you should be able to run the following command to create a container indicating that we want to use folder `/root/tiny-container` as our new root folder and launch the `ts` executable (that is inside that root folder) as our shell:

```terminal
$ ./tc -root=/root/tiny-container -shell=./ts
Creating container...
Launching shell session..
Tiny shell started
ts: _
```

By now we are on our tiny shell program (not on `bash`) that is running inside the container. Let's run a few commands to see what we get and how they compare to what we saw when we ran those commands outside our container:

```terminal
$ ./tc -root=/root/tiny-container -shell=./ts
Creating container...
Launching shell session...
Tiny shell started
ts: _

ts: hostname
Hostname: tinyhost

ts: env
tiny_demo=something tiny

ts: cd /
ts: ls 
Files in: /
	README.md
	build.sh
	createBusybox.sh
	tc
	tinyContainer.go
	tinyShell.go
	ts

```

Notice how our `hostname` is now "tinyhost" instead of "ubuntu-20-test" and our `env` variables are different than the ones outside the container. In fact, if you open a new terminal in your Linux machine and re-run the `hostname` and `env` commands you will get the values from the Linux machine itself (like you did before) not the ones from the container, likewise you will have access to all the files in the Linux machine, where as the container is limited to only those files in our new root.

As simple as this example is, it shows how we are now *running in an isolated space* and we cannot access the file system or the environment variables from the Linux host at all.

Now, limiting access to the file system is just part of the story, by using other system calls you can also sandbox network access and allow only certain kind of traffic to go back and forth between the Linux host and the container. This would be really useful if you wanted for example to run a database or web server in the container and make it available to the host. The example in the Mr. Jain's book cover those topics.

### Using BusyBox to provide the shell
Another way to run a shell inside of our container is to use third party program that implements the shell in all its glory (unlike my `tinyShell.go` that only emulates poorly a few commands.) This is the approach that Mr. Jain follows in his book.

One tool that you can use for this purpose is [BusyBox](https://www.busybox.net). BusyBox provides a single binary executable with many of the commands that you would expect to find in a typical Linux installation and their implementation is complete or close to complete. You can use this program to provide the shell and have a richer experience inside our tiny container.

Script [createBusybox.sh](https://github.com/hectorcorrea/tiny-container/blob/main/createBusybox.sh) in the GitHub repo shows how to get this executable and create symlinks inside a folder `./bb_root/bin`. Once you run this script you should be able to create a tiny container like we did before but launch the shell program that BusyBox provides:

```terminal
$ ./tc -root=/root/tiny-container/bb_root -shell=/bin/sh
Creating container...
Launching shell session...
/ # 
```

Inside this shell session you should get the same values for `env` and `hostname` as you did before, but if you run `ls` you should see a `bin` folder with tons of Linux utilities, all of them provided by BusyBox. 

```terminal
$ ./tc -root=/root/tiny-container/bb_root -shell=/bin/sh
Creating container...
Launching shell session...
/ # ls
bin

/ # ls bin/
diff    hexdump  grep vi
# and many many more
```

There is even `vi` so you could type `vi hello.txt` and edit a text file and save it, all of it inside your container. Pretty neat.

If you are interested in this approach the talk [Minimal Containers](https://www.youtube.com/watch?v=gMpldbcMHuI) by Brian Redbeard is a great way to learn more about how to use tar balls as containers. Redbeard uses [Buildroot](https://buildroot.org/) instead of BusyBox but the ideas are similar. 

## Wrapping up
Learning about containers from the kernel point of view has been very interesting to me. I am not thinking of building the next Docker or anything like that, but this kind of knowledge helps me understand better how hosting companies are using containers to power very large virtualization environments with hundreds of containers in a single server machine, do it relatively fast, and in a way that is secure. 