# Running Visual Studio inside Mac OS X
Lookie here...I am running Microsoft Visual Web Developer Express on my MacAir!

<img src="https://hectorcorrea.com/images/osxwin7.png" alt="Windows 7 inside OS X Lion" width="80%" />

When I bought [my MacAir last year](https://hectorcorrea.com/blog/ruby-development-on-the-mac-os-x/15) to start learning Ruby and Ruby on Rails I wasn't sure how much I was going to really use it. Overtime, I've gotten familiar with it and little by little it has become my main machine at home to do pretty much everything including Ruby programming, managing my music, pictures, and documents, and web browsing. It's gotten to the point that the only reason I power up my Windows laptop at home is when I need to update one of my personal ASP.NET projects or research some C# topic in particular.

My Windows laptop at home is a nice small Lenovo 3000 v200 that is about 4 or 5 years old. It's a very nice laptop but compared with the MacAir it feels like a clunker. There isn't anything wrong it per-se, it just feels like an old machine.

As time went on I started pondering how to run Visual Studio inside my Mac so that I don' have to rely on two machines. There are many options to host virtual machines inside OS X including the built-in Boot Camp software that comes with the Mac, VMWare, and VirtualBox.

In my case I decided to use [VirtualBox](http://www.virtualbox.org/) to create a Windows 7 virtual machine where I can install Microsoft Visual Web Developer Express 2010.

The screenshot at the beginning of this blog post shows Visual Web Developer Express running inside a Windows 7 virtual machine hosted on OS X.

I decided to use VirtualBox over the other virtualization options for two main reasons. One is that it's free (that's hard to beat) but also because is multi-platform which allowed me to create the virtual machine on my Windows machine (where I have plenty of disk space) and once it was ready just transfer VM to my Mac and power it up there.

Although I configured the virtual machine that emulate a hard drive with 20 GB the VM itself only uses 12.5 GB with Windows 7 and Visual Web Developer Express installed. That was perfect for me as my MacAir has a very small solid state drive to begin with. Being able to create the virtual machine on a different machine with plenty of disk space was a big plus for me.

Now I am able to work on all my home projects on the same machine regardless of whether I want to program in Ruby or in ASP.NET. I am not quite ready to sell my Windows laptop yet, though. We'll see if that changes in the future.