# Using SSH without a password
This blog post describes the steps that I used to configure my OS X machine to SSH to a remote Linux machine without having to enter my password every time. Although there are many web sites that describe this process in detail I found that most of them assume that you know your way around a Linux machine. Since that's really not the case for me, I decided to write the steps from a Linux-newbie point of view with the hope to help others that might be on the same boat.

I tested the steps on this blog post connecting from a Mac Air running OS X 10.8.2 to a Linux OpenLogic CentOS 6.2 virtual machine running on the Windows Azure cloud. I suspect the steps apply to most Linux distros but I have no way of confirming it. 

Before we dive into details let me define a few terms that you'll see throughout the blog post.

* Local machine: (or local host) represents your Mac OS X machine. 
* Remote machine: (or remote host) represent your Linux machine.
* linuxUser: represents the user name that you use to SSH to your Linux machine.
* linuxMachine.somedomain.com: represents the name of the remote machine. 

Also, the steps on this blog post assume that you can successfully login to your remote host by issuing `ssh linuxUser@linuxMachine.somedomain.com` and typing your Linux password. If that's the case read on.


## Prep work

The first thing that we'll do is to make sure there is an `~/.ssh` directory in the remote machine and is configured with the proper access rights. From your local host SSH to your remote host with the following commands:

```terminal
ssh linuxUser@linuxMachine.somedomain.com
[enter your password]
```

Once you are logged into your remote host create the `~/.ssh` directory. It's very important to make sure this directory is created with limited access, hence the `-m 0700` parameter in the call below. The `-m 0700` makes sure that only the owner of this directory has access to view its content. Make sure you don't skip this step. *SSH is very picky and won't work if the access is not limited on this directory*.

```terminal
cd
mkdir -m 0700 .ssh
ls -la
```

The call to `ls -la` should show you a bunch of files and directories and the newly `.ssh` directory with limited access. You should see "drwx------" as the access for this new directory. If the .ssh directory already exists on your remote machine, you can still change its access rights by issuing `chmod 0700 .ssh` instead of the create directory command.

Logout of your remote host.


## Generating an SSH key

Let's now generate the key file for SSH access. Issue the following command from your local machine.

```terminal
ssh-keygen -f linux_rsa -C linuxUser
[enter passphrase]
[re-enter a passphrase]
```

This command will generate two SSH key files called `linux_rsa` and `linux_rsa.pub` under your `~./ssh` directory. These files contain you private and public keys respectively. The `-C` parameter is just a convenience to add a comment to the public key file and, to the best of my understanding, it has no other purpose. These two key files are plain text files and you can see their content with the cat command (e.g. `cat linux_rsa.pub`)

The next step is to copy your public key to the remote machine. We can do this with the secure copy program (scp) with the following commands from your local machine:

```terminal
cd ~/.ssh
scp linux_rsa.pub linuxUser@linuxMachine.somedomain.com:.ssh/authorized_keys
[enter your password]
```

This will copy the `linux_rsa.pub` file to the remote host under the `.ssh` directory and name it `authorized_keys`. Be aware that *if you already have a file called authorized_keys in your remote machine the previous step will overwrite it*. If that's the case you might want to name it something else (say authorized_keys2) and then manually append it to your existing authorized_keys file. I doubt this will be your case since you wouldn't be reading this blog post if you already a have working authorized_keys file in your remote machine...but you've been warned :)

Our next step is to make sure this file has very limited access rights on the remote machine (similar to what we did for the .ssh folder a few steps before.) To change the access rights for this file, SSH to the remote host again and issue the following commands.

```terminal
ssh linuxUser@linuxMachine.somedomain.com
[enter your linux password]

cd ~/.ssh
chmod 0600 authorized_keys 
ls -la
```

The `authorized_keys` file should show limited access when listed. You should see the following access for this file "-rw-------". 

Logout of the remote machine.


## Adding the key to the authentication agent

So far we have generated private and public keys and have copied the public key to the remote host. Now we need to configure our local OS X machine to automatically use these credentials.

There are two steps that we need to follow. First we make sure sure the `ssh-agent` is running and then we register our keys with it via the `ssh-add` utility.

```terminal
cd ~/.ssh
eval `ssh-agent`
ssh-add -K linux_rsa
[enter passphrase]
[reenter passphrase]
```

Notice that the *passphrase* that you will enter here is the one that you entered when generating the key files with the `ssh-keygen` utility above. 

At this point you should be all good. Try SSHing to the remote host again and you should *not* be prompted to enter your password.

```terminal
ssh linuxUser@linuxMachine.somedomain.com
(you should be logged in without having to enter your linux password)

logout
```

In order for the authentication to work next time you launch the Terminal in your OS X you might need to add the following line to your `~/.bash_profile` file.

```code
eval &#96;ssh-agent&#96;
```

It seems that the `ssh-agent` is not running by default when you launch the Terminal and therefore it won't automatically pass your credentials next time you open the Terminal. It's very important to run the ssh-agent through the `eval` command as shown above.

**Update:** It turns out the Keychain Access tool that comes with the Mac OS X can take over this steps with the ssh-agent. This [blog post by Dave Dribin](http://www.dribin.org/dave/blog/archives/2007/11/28/ssh_agent_leopard) has more information about the ssh-agent and its relation to Keychain Access.


## One final tweak

If everything worked out fine at this point you can SSH to your remote host without having to enter your password. However, the SSH command that you have to issue is still pretty darn long

```terminal
ssh linuxUser@linuxMachine.somedomain.com
```

You can create an alias for this so that you don't have type as much. Edit your `~/.ssh/config` file with your favorite text editor and add the following to it:

```code
host linux
    hostname linuxMachine.somedomain.com
    user linuxUser
```

This will configure SSH so that when you type `ssh linux` it will automatically try to login with the username and domain in the config file.

```terminal
ssh linux
(you should be logged in without having to enter your linux password)
```


## References

I learned most of the steps described in this blog post from the book [bash Cookbox](http://www.amazon.com/Bash-Cookbook-Solutions-Examples-Cookbooks/dp/0596526784) by Carl Albing, JP Vosen, and Cameron Newham. The book goes into far more detail on each of the steps that I described in this post. Check out chapter 14 "Writing Secure Shell Scripts" for more information.