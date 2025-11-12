# Go executables and machine architecture
*Posted on 2025-11-11*

One of my favorite features of the Go programming language is that it can produce executables that I can copy to another machine and run without having to worry about dependencies on the target machine. To make things even better I can build executables for other operating systems, like Linux or Windows, from my Mac.

For example, to build the executable for one of my projects (`marcli`) I run the following command on My Mac and I get a Mac executable:

```
go build -o marcli
```

But I can also run the following command (again, from my Mac) and get instead a Linux executable:

```
GOOS=linux go build -o marcli_linux
```

Notice the `GOOS` parameter is set to "linux" in the second example.


## The operating system
The `GOOS` parameter in the previous command is what tells Go what kind of executable I want to build. There are many operating systems that the Go language supports out of the box including Mac OS (`GOOS=darwin`), Linux (`GOOS=linux`), and Windows (`GOOS=windows`)

You can get a list of all the supported operating systems by running `go tool dist list`, the results looks more or less like the one below (I listed only a few of the 48 options that the command shows):

```
darwin/amd64
darwin/arm64
linux/386
linux/amd64
linux/arm
linux/arm64
linux/mips
linux/riscv64
windows/386
windows/amd64
windows/arm64
```

The first part of each line shows the operating system (e.g. `darwin` or `linux`) and the second part is the architecture (more on this later).

The operating system is pretty obvious and most developers know what kind of operating system we are using, however the architecture is just as important for an executable and most of us are usually happily unaware of the architecture of the chip inside our machines (at least I am).


## The architecture of the machine

The [architecture of the machine](https://en.wikipedia.org/wiki/Computer_architecture) is determined by the chip inside of it and lucky for us we usually don't have to worry about it since the operating system knows how to deal with it. However, if we are going to build an executable for another computer we need to tell the Go compiler what kind of architecture we want for that executable.

For example, when I build an executable on my Mac with `go build -o marcli` internally the Go compiler is using two default values to determine the operating system (`GOOS`) and architecture (`GOARCH`) I want for the executable, *even if I don't specify one*.

For example in my new Mac if run `go env GOOS GOARCH` I get the following values:

```
darwin
arm64
```

However, if run the same command on an older Mac I get the following values:

```
darwin
amd64
```

Notice that my new Mac uses the [ARM64](https://en.wikipedia.org/wiki/AArch64) architecture whereas my old Mac uses the [AMD64](https://en.wikipedia.org/wiki/X86-64) architecture.

**Note:** For some weird historical reasons the AMD64 architecure is sometimes referred as "x86-64" and because of this some tools might return "amd64" whereas others will return "x86-64", for the purposes of this post they are interchangeable.

The fact that each of my Mac has different defaults values for the `GOARCH` value means is that if build an executable with just `go build -o marcli` I will get a different kind of executable depending on the Mac that I use: my newer Mac will produce a binary for the ARM64 architecture whereas my older Mac will default to the AMD64 architecture.

Luckly for us we can generate executables for specific architectures regardless of the architecture of our machine. For example I can run the following commands on my Mac:

```
GOOS=darwin GOARCH=amd64 go build -o marcli_amd64
GOOS=darwin GOARCH=arm64 go build -o marcli_arm64
```

and I will get two executables, both of them are for the Mac operating system (`GOOS=darwin`) but one of them is for Mac computers with the AMD64 architecture (`GOARCH=amd64`) whereas the other is for Mac computers with the ARM64 architecture (`GOARCH=arm64`). And I can do this from any of my Mac computers regardless of their own architecture.

I can inspect a given executable with the `file` command. For example file `marcli_amd64` will show "Mach-O 64-bit executable x86_64" whereas `file marcli_arm64` will show "Mach-O 64-bit executable arm64".


## What kind of architecure does my computer have?

As I mentioned earlier the architecture of the machine is something that depends on chip inside. On a Mac you can find the architecture with the [uname](https://stackoverflow.com/questions/65259300/detect-apple-silicon-from-command-line) command.

On my new Mac I get "arm64":

```
$ uname -m
arm64
```

whereas on an old Mac I get "x86_64" (remember that "x86-64" is the equivalent to "amd64" for our purposes).

I can also find the *chip* my computer has by clicking on the Apple icon (&#xF8FF;) on the upper left corner and selecting "About this Mac". The information displayed contains something like "Chip: Apple M4" or "Chip: Intel Core m3". From the chip information is possible to figure out the actual architecture, it's kind of a pain, but a few searches on the web will get you there. Wikipedia says that the [Apple M4](https://en.wikipedia.org/wiki/Apple_silicon) machines use the AMR64 architecure and the [Intel](https://en.wikipedia.org/wiki/X86-64) machines use the AMD64 architecture.


## Building executable for other operating systems

One thing that I recently [found out the hard way](https://github.com/hectorcorrea/marcli/issues/20) was that *the architecture of my machine is used as the default architecture when I build an executable for another operating system*.

For example, if I am on an **older Mac** with the AMD64 architecture (`GOARCH=amd64`) and I build a Linux executable without specifing an architecture the Go compiler will attempt to use the same architecture for the Linux executable (notice the reference to the "x86-64" in the command below):

```
$ go env GOOS GOARCH
darwin
amd64

$ GOOS=linux go build -o marcli_linux
$ file marcli_linux
ELF 64-bit LSB executable, x86-64 ...
```

But if I am on a **newer Mac** with the ARM64 architecture (`GOARCH=arm64`) the exact same command will produce a different executable: it will produce an executable for Linux but for the ARM64 architecture (notice the "ARM aarch64" in the command below):

```
$ go env GOOS GOARCH
darwin
arm64

GOOS=linux go build -o marcli_linux
file marcli_linux
ELF 64-bit LSB executable, ARM aarch64
```

In hindsight this makes sense, without me indicating an architecture (`GOARCH`) the Go compiler takes a guess. The problem with this is that the fact that I have a Mac with the AMD64 architecture does not mean that the Linux machine where the executable will be used will also have this kind of architecture. But then again, there is no guarantee that the Linux machine will have an ARM64 architecture either.

The takeaway message is to always specify both the operating system (`GOOS`) and the architecture (`GOARCH`) of the executable that you are building. I think using `GOARCH=arm64` and `GOARCH=amd64` covers a lot of cases for Mac and Linux but then again this kind of knowledge is not my forte.


## Universal binaries on the Mac OS
It is a bit of a pain to have to build executables taking into consideration the architecture of the target machine and I think it is even worse for users to have to choose what kind of executable they want to download. I mean how many of us know whether we want the ARM64 or AMD64 executable of a given tool.

On the Mac is possible to build *universal binaries* with the `lipo` tool that comes [built-in the Mac OS](https://dev.to/thewraven/universal-macos-binaries-with-go-1-16-3mm3).

For example we can build two different executables:

```
GOOS=darwin GOARCH=arm64 go build -o marcli_arm64
GOOS=darwin GOARCH=amd64 go build -o marcli_amd64
```

and then combine them to create an executable that will work regardless of whether you have an old Mac with the AMD64 architecture or a new Mac with the ARM64 architecture:

```
lipo -create -output marcli_universal marcli_amd64 marcli_arm64
```

If we inspect this executable with `file marcli_universal` we can see that it reports both architectures:

```
marcli_universal: Mach-O universal binary with 2 architectures: [x86_64:Mach-O 64-bit executable x86_64] [arm64]
marcli_universal (for architecture x86_64):	Mach-O 64-bit executable x86_64
marcli_universal (for architecture arm64):	Mach-O 64-bit executable arm64
```

A disadvantage of this approach is that the universal executable will be twice a large as each of the individual executables. Depending on your situation this might be a problem or not, but I think it removes such a burden from the end user that it is worth considering.

Another thing to consider is that `lipo` is a tool for Mac OS, I am not sure if there are equivalents for other operating systems.
