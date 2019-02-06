---
title: Bash Essentials
date: 2019-02-03 15:50:19
tags: linux
---

When starting out with the bash shell, there are a few essential concepts beyond the basic commands that one must be aware of.  Beyond [the basics]() and [user permissions](https://zachgoll.github.io/blog/2019/user-permissions/), one must learn about environment variables, aliases, and some more advanced commands relating to topics of networking, process management, ssh, and scripting.

## Environment variables

An environment variable can come in two sizes--global or local.  A global environment variable is one that is set upon initialization of a shell and can be used across all your shells.  A local environment variable is one that is set during a shell session and is erased when the shell is closed.  To create a local environment variable, just export it in your shell.

```bash 
export MY_VARIABLE="some value"
```

You can use this as long as the shell is still open.

```bash
echo $MY_VARIABLE # some value 
```

As soon as you close the shell, this will no longer be a variable.  If you want to create a global variable, you will need to export it in one of your bash configuration files.  It is recommended that you place it in the `~/.profile` file as described in [this tutorial](https://zachgoll.github.io/blog/2019/user-permissions/).

```bash
# ~/.profile

export GLOBAL_VARIABLE="some value"
```

To see all of the environment variables in your current environment (both local and global), you can type the following command.

```bash 
env
```

## Shell Variables 

Oftentimes, users will get confused when talking about _environment_ variables and _shell_ variables.  Regardless of whether the environment variable is local or global, all the scripts running in that shell session will have access to the variable.  With a shell variable, other scripts will not have access to it.  The following is how we declare a shell variable.

```bash 
MY_SHELL_VARIABLE="some value"

echo $MY_SHELL_VARIABLE  # some value 
```

This variable will not be available to any script until we export it and make it an environment variable.

```bash
export MY_SHELL_VARIABLE
```

## PATH variable

The most important global environment variable that you must set is the `PATH` variable.  This is the variable that tells the bash shell where to find different executable files and scripts.  The shell will check the directories listed in the `PATH` variable for the script you are trying to find.  Here is an example path.

```bash 
export PATH=/usr/local/bin:/usr/bin:/bin
```

With this example path, when you try to run a script in the terminal such as `cat`, bash will first look in the `/usr/local/bin` for this command.  If it doesn't find it there, it will look in `/usr/bin`, and finally, `/bin`.  If you need to modify the path, you can update it in your `~/.profile` like so: 

```bash 
export PATH=/home/zach/script-location:$PATH
```

This will add the `/home/zach/script-location` directory to the beginning of the `PATH` variable.  If you want to add it at the end, you can change the line to this: 

```bash 
export PATH=$PATH:/home/zach/script 
```

With this new knowledge, we can create our own scripts and add those scripts to our path so we can run them automatically.  Let's create a directory in our home folder that will hold all our custom scripts.

```bash 
cd ~ 
mkdir bin 
touch bin/my-simple-script
```

Add the following to `my-simple-script`.

```bash
#!/bin/bash 
echo "I am a useless script for tutorial purposes only"
```

Now make the script executable.

```bash 
chmod 775 my-simple-script
```

We need to add the new `bin/` directory to our path.  Open `~/.profile` (or wherever you define your `PATH` environment variable), and add the following line.

```bash 
export PATH=$PATH:~/bin
```

Finally, you need to source `~/.profile` to activate this new change.

```bash
source ~/.profile
```

You can now run your simple script from the command line by typing the name of it.

```bash 
my-simple-script  
# I am a useless script for tutorial purposes only
```

## Common Commands 

Aside from basic commands such as `cd`, `ls`, `mkdir`, `touch`, etc., there is a short list of commands that I think every bash user should know about and attempt to use on a regular basis.

* grep
* less
* find
* awk/sed
* exec
* gunzip
* tar

### grep

Grep is a tool that will find certain patterns or words in one or more files.  It comes in two versions--BSD and GNU.  Mac computers will come with the BSD version while linux computers will come with the GNU version.  For our intent, these versions are identical, but if you ever run into issues cross-platform, be sure to check that the grep command you are executing is supported in your grep version (certain types of regular expressions not supported in BSD grep).

You can think of grep as the "search" tool built-in to the command line.  In today's computing environments, there are plenty of search tools and in most cases, I would opt to use the search tools provided to me.  For example, in the Visual Studio Code Editor, I can use the search

## Advanced Bash 

Not all of the following topics are solely bash concepts, but they are important to be aware of even if you are not going to be using them on a daily basis.  The breadth of concepts go to show the true power of the bash shell.  The topics are as follows.

* Bash Scripting
* Regular expressions (used with scripting)
* Virtual Machines and SSH 
* Networking on the command line
* Process management
* System Management