---
title: Bash Essentials
date: 2019-02-03 15:50:19
tags: linux
---

_This is the full video.  Individual video parts posted at beginning of each section._

<iframe width="560" height="315" src="https://www.youtube.com/embed/o6kks8109Vg" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

When starting out with the bash shell, there are a few essential concepts beyond the basic commands that one must be aware of.  Beyond [the basics]() and [user permissions](https://zachgoll.github.io/blog/2019/user-permissions/), one must learn about environment variables, aliases, and some more advanced commands relating to topics of networking, process management, ssh, and scripting.

# Environment variables

<iframe width="560" height="315" src="https://www.youtube.com/embed/5e-kS5LkByc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

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

<iframe width="560" height="315" src="https://www.youtube.com/embed/yCTnihfbPCo" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Aside from basic commands such as `cd`, `ls`, `mkdir`, `touch`, etc., there is a short list of commands that I think every bash user should know about and attempt to use on a regular basis.

* grep
* awk/sed
* less
* find and exec
* gunzip
* tar

### grep

Grep is a tool that will find certain patterns or words in one or more files.  It comes in two versions--BSD and GNU.  Mac computers will come with the BSD version while linux computers will come with the GNU version.  For our intent, these versions are identical, but if you ever run into issues cross-platform, be sure to check that the grep command you are executing is supported in your grep version (certain types of regular expressions not supported in BSD grep).

You can think of grep as the "search" tool built-in to the command line.  In today's computing environments, there are plenty of search tools and in most cases, I would opt to use the search tools provided to me.  For example, in the Visual Studio Code Editor, I can use the search bar to search through all the files in my project for a specific word or even regular expression, and I will get results listing the files that the expression was found in, what line it was found on, etc.

{% asset_img vscode-search.png %}

In this example, I was searching my entire blog for the word "grep" and found it in 3 separate files.  But what if VSCode is not availabe to you?  Maybe you are managing a remote machine and you simply cannot access this awesome search feature?  In that case, you are going to need to use grep.  The basic syntax for using grep is the following: 

```bash 
grep [all your options] "text to search for" <file-to-search-in>
```

For example, if I wanted to search the `/etc/passwd` file (where all the users on our system are stored) for my name, I could do it like so.

```bash
# Must use sudo because this file is protected
# The -i flag will make my search case insensitive 
sudo grep -i "zach" /etc/passwd 

# zach:x:1000:1000:Zach,,,:/home/zach:/bin/bash
```

Although this tutorial is not meant to cover regular expressions in detail, I could also search by a regular expression.  Maybe I wanted to match any user that had a name equaling 3 characters.  I would perform the following search while adding the `-E` flag for "extended regular expressions".

```bash 
sudo grep --color -E "^[a-z]{3}:" /etc/passwd

# Output --- 
#bin:x:2:2:bin:/bin:/usr/sbin/nologin
#sys:x:3:3:sys:/dev:/usr/sbin/nologin
#man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
#irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
#gdm:x:121:125:Gnome Display Manager:/var/lib/gdm3:/bin/false
```

`bin`, `sys`, `man`, `irc`, and `gdm` are all three letter usernames.  If your output does not print in color, you can add the `--color` flag to your command.

Another way to use grep is through piping.  We can pip the stdout of one command to the stdin of grep.  The same command we just wrote about can be modified and written like so: 

```bash 
sudo cat /etc/passwd | grep --color -E "^[a-z]{3}:"
```

Again, the `grep` utility is useful for quick searches when you do not have access to your normal text editor like VSCode.

### awk and sed

Nowadays, `awk` and `sed` are contentious topics, and in this section, I hope to clarify _why_ this is the case and introduce the two commands' basic usages.  The reason these two commands are contested frequently is due to the better alternatives that we have today.  Both of these tools are considered text editors because they can find _and_ replace parts of a file or files.  

Sed is a "stream editor" (looks at each line character by character) and is not considered a full programming language.  It is great for little find and replace operations across one or more files.  Again, I would recommend just using a built-in find/replace utility if it is available (like in VSCode), but sometimes this is not accessible.

Awk considered a computationally complete programming language and is catered more towards large data that is delimiter separated.  For example, the `awk` tool would be good at parsing through a csv file, or a file separated by spaces because it reads each line in chunks rather than characters.

The reason these utilities are highly contentious is because anything you can do in sed or awk can be done in Perl or Python in a simpler manner.  Perl and Python are far more advanced and usable scripting languages.  Many will argue the superior performance of sed and awk in certain situations, but for all practical purposes, it is more often the case that Perl or Python will be the better solution.

Not to beat a dead horse here, but again, if you do not have access to Python or Perl (remember, you must download these to a machine while sed/awk are usually installed already), you are going to need to learn how to use sed and awk.  Now the question becomes--what is the difference?  Why would I use sed over awk or vice-versa?  Here are few quick rules of thumb: 

* For simple text transformations (like find/replace), use sed.
* For simple formatting transformations, use awk
* For complex formatting/text transformations, use awk

Let's take a look at an example of each and cover the basic usage of both tools.  We will start with a simple find/replace operation with sed.  For these examples, I will be using a sample file of Apple Inc. stock data over the last 3 months called `aapl.csv`.  Here is the data: 

```
Date,Open,High,Low,Close,Adj Close,Volume
2018-11-06,201.919998,204.720001,201.690002,203.770004,203.061493,31882900
2018-11-07,205.970001,210.059998,204.130005,209.949997,209.219986,33424400
2018-11-08,209.979996,210.119995,206.750000,208.490005,208.490005,25362600

... omitted for brevity ...

2019-02-04,167.410004,171.660004,167.279999,171.250000,171.250000,31495500
2019-02-05,172.860001,175.080002,172.350006,174.179993,174.179993,36066500
```

Say I wanted to replace all the commas with spaces instead.  I could do this quickly with sed.

```bash
sed 's/,/ /g' AAPL.csv
```

```
Date Open High Low Close Adj Close Volume
2018-11-06 201.919998 204.720001 201.690002 203.770004 203.061493 31882900
2018-11-07 205.970001 210.059998 204.130005 209.949997 209.219986 33424400
2018-11-08 209.979996 210.119995 206.750000 208.490005 208.490005 25362600

... omitted for brevity ...

2019-02-04 167.410004 171.660004 167.279999 171.250000 171.250000 31495500
2019-02-05 172.860001 175.080002 172.350006 174.179993 174.179993 36066500
```

This will print the output of the sed transformation.  If I add the -i flag to the command, it will edit the file.

```bash
sed -i 's/,/ /g' AAPL.csv
```

Now that we have the file separated by spaces, we can look to `awk` to do some better summarization.  This data is a bit confusing in its current state because it has multiple versions of the stock price (open, high, low, close, adjusted).  I just want to see the date, open, and volume.  To get that, I can use awk.

```bash 
awk -F " " ' BEGIN { print "Date\t\tPrice\t\tVolume" }; NR > 1 { print $1 "\t" $2 "\t" $7 } ' aapl.csv
```

```
Date            Price           Volume
2018-11-06      201.919998      31882900
2018-11-07      205.970001      33424400
2018-11-08      209.979996      25362600

... omitted for brevity ...

2019-02-04      167.410004      31495500
2019-02-05      172.860001      36066500
```

I know this command looks overly complex and difficult to learn and the purpose of this tutorial is not to make you an `awk` expert, but let's take a moment to walk through the basic syntax of an awk command.  Once you know the basic syntax, a few Google searches will usually get you to the awk one-liner that you need for your given situation.

Awk is a bit of a tough command to learn because it is more of a programming language than a command line tool.  To learn what the command above means, let's write it into a file for greater clarity.  I have created a file called `awk-example.sh` and placed the above command in it with better formatting.

```bash 
# This is an awk comment
# Before processing any text, we want to set the FS variable (what our file is separated by), 
# the OFS variable (what our output is separated by), and then print a header for our output.
BEGIN {
        # Notice how each line is ended with a semicolon -- similar to C programming language
        FS=" ";
        OFS="\t";
        print "Date\t\tPrice\t\tVolume";
};

# NR > 1 means that we only want to print the following bracket enclosed values for lines that are greater
# than line 1.  In other words, we are omitting the first line.
NR > 1 {
        # We want to print the first, second, and seventh value in each line.
        print $1, $2, $7;
};
```

We can run this file like so: 

```bash
# Outputs the same thing as 
# awk -F " " ' BEGIN { print "Date\t\tPrice\t\tVolume" }; NR > 1 { print $1 "\t" $2 "\t" $7 } ' aapl.csv
# This reads the awk-example.sh file and executes it against aapl.csv
awk -f awk-example.sh aapl.csv
```

As you might have noticed there are some built in variables to awk which include `FS` (file separator), `OFS` (output separator), `NR` (number of rows), and many more that can be [found here](https://www.tutorialspoint.com/awk/awk_built_in_variables.htm).  We can use these variables rather than the command line flags.  For example, instead of writing `-F " "`, we can just set the `FS` variable in our awk file.

Learning `awk` entirely would take far more time than is warranted here in this tutorial, but if you can remember the basic syntax, you'll be good to go.  Ultimately, each awk command is a series of keywords and outputs.  The command we just ran is shown below in this format.

```bash
# awk executable  Options   Keyword  What to print based on keyword          Keyword   What to print based on keyword file to run
awk               -F " " '  BEGIN    { print "Date\t\tPrice\t\tVolume" };    NR > 1    { print $1 "\t" $2 "\t" $7 } ' aapl.csv
```

Awk can do much more than what has been shown including editing files.  A great introductory tutorial can be [found here](https://likegeeks.com/awk-command/).

When push comes to shove, you generally do not need to be proficient in sed nor awk.  Perl/Python can do the same exact things with simpler syntax.  That said, knowing the basic usage of each can significantly speed up your workflow during those few moments where they do become necessary.  They can also become useful when you are writing a bash script and need a built-in text editor.  It would be inconvenient to break out of a bash script, run a python script, and then start your script again, so sed and awk provide nice alternatives to this workflow.

### Less

Less is one of those command line utilities that you are probably not using and should be.  I find myself scrolling through large output all the time and forget that there is a command line utility that makes this a lot easier.  Less is simple to use, and works very similar to vim.  You can run less in two ways: 

```bash 
# Pipe output into less
cat some-large-file.txt | less 

# Use less directly
less some-large-file.txt
```

Once you are in less, your biggest asset is the `h` option.  Type `h` and you will receive a help panel with all the possible commands you can run.  I will go through some of the most useful and common.

* To scroll up a line, press `k` (like vim)
* To scroll down a line, press `j` (like vim)
* To go to end of file, press `G`
* To go to beginning of file, press `g`
* To search for "some text", type `/some text` (to search forward) or `?
* To go forward an entire window, type `f` or spacebar
* To go back a window, press `b`

Just like grep, awk, and sed, scrolling and searching through output can be done in advanced text editors like VSCode fairly easily and if you have the opportunity to use these more advanced tools, go ahead and do so.  Using the `less` command is simply for those moments when those tools are not available to you.

### find and exec

The find command is eerily similar to grep at first glance, but covers an entirely different use case.  You would need to use the find command when you want to search for certain files within the entire filesystem.  While grep searches for text in a file or a specified output, find searches explicitly for files in the filesystem.  Why is this useful?  Many times this command could be used to find the path where a specific executable is stored on the filesystem so you can edit, move, or remove it.  Maybe you installed a version of Python a long time ago and cannot remember where you downloaded it.  It is a common problem to have conflicting installations of Python on your machine.  It is also common to struggle to entirely remove Python from your machine.  Other uses for find could include: 

* Find all of the .png files on your computer
* Find all documents modified by some user in the last 7 days
* Find any file that has a certain permission set

As you can see, the possibilities here are endless and if used to its potential, the `find` command can solve a lot of issues that you never knew it could.  A quick example: 

```bash 
find / -type f -size +1G
```

This command will list all of the files on your computer that are greater than 1 Gigabyte in size.  If you're low on disk space, this command will be your good friend and help you find those files that are taking up unecessary space on your computer.

The basic syntax for the find command is: 

```bash
find <directory to search> [options] <file name to search>
```

Here are a few rapid-fire find commands to get you started.

```bash 
# Finds all jpg files on your system
find / -type f "*.jpg"

# Finds all files modified in the last day
find / -type f -mtime 1

# Find all files that belong to zach
find / -type f -user zach

# Find all files with 777 permissions
find / -type f -perm 777

# Find all files that start with the word config
find / -type f -name "config*"
```

These are just a couple of the thousands of possibilities here.  Furthermore, you can use the `exec` command to take the `find` command to a new level.  Instead of just finding a bunch of files, you can perform operations on them.  Although very powerful, this also can be very dangerous if you are not careful.  The exec function will do as you might guess and execute a command on every single file that was found by `find`.  So if you combine the `find` and `rm` command together using `exec`, you may delete a bunch of files.  So before we get into it, be careful!

Here is a simple find command that will find all the jpg files in our home directory.

```bash
find ~/ -type f -name "*.jpg"
```

If we wanted to copy all the matches to a `~/pictures-backup` folder, you can add the exec command.

```bash 
find ~/ -type f -name "*.jpg" -exec '{}' ~/pictures-backup \;
```

You might be wondering what the `'{}'` and `\;` pieces of this command mean.  If you use your new skills, you can find more information about the `exec` command by piping the find man pages into less and searching for the word "exec".

```bash 
man find | less 
/-exec
n # repeats the search for -exec and finds next occurrence of it
```

In these man pages, you can see that the `'{}'` is where the files that were found are placed in the command and `\;` is a character that tells the `exec` script to stop executing.  The backslash is just a safety mechanism.  You could also use `';'`.  

After running this command, you now have all the jpg pictures in your entire home directory copied to a centralized backup repository!  You can begin to see how powerful this new command is!

### tar, gzip, gunzip

These utilities are fairly simple and used to compress and decompress files.  Many times when we download a software release or series of large files or images, we will get them in the `.tar`, `.gz`, or even `.tar.gz` formats.  `tar` and `gz` are slightly different.  `tar` is an archive format while `gz` is a compressed format.  Most of the time, we an rely on our computer's file explorer to be able to handle these formats, but sometimes, we need to unzip or decompress them on the command line (think remote server).  Here are the most common commands.

```bash
# Create a .tar format archive
# c is create mode, v is verbose output, f is tar format
tar cvf archive.tar file1 file2 file3 ... filen

# List what is in .tar archive
# t is list mode
tar tvf archive.tar

# To unpack a .tar file
# x is extract mode, v is verbose output, f is tar format
tar xvf archive.tar

# Compress a file or archive
gzip archive.tar # creates archive.tar.gz

# Uncompress 
gunzip archive.tar.gz
```

There a several more options you can explore, but these commands should get you started with compression/archive utilities.

# Advanced Bash 

Not all of the following topics are solely bash concepts, but they are important to be aware of even if you are not going to be using them on a daily basis.  The breadth of concepts go to show the true power of the bash shell.  The topics are as follows.

* Regular expressions (used with scripting)
* Bash Scripting
* Virtual Machines and SSH 
* Networking on the command line
* Process management
* System Management

## Regular Expressions

<iframe width="560" height="315" src="https://www.youtube.com/embed/NhcaKdt3NdM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

To me, regular expressions are often made far more complicated than they need to be. Sure, there are a lot of options and little details to learn regarding regular expressions, and on top of that, there are many different flavors of regular expressions (python, extended, rust, etc.).  Despite this, there are only a few core concepts that one must understand about regular expressions that will then translate in the ability to use any flavor of regular expressions effectively.

Regular expressions exist because a literal text searching program is sometimes not good enough.  Let me give you an extremely practical example from my own work to explain.

I recently wrote a script in Microsoft Excel VBA that executed commands from an external library.  The code of this external library was not available for me to see, and therefore, I had to use it with limited control.  As a result of this, the library would open up a new Excel workbook for every function call I made.  In each workbook, there was data that I needed to copy and paste into my main workbook, but in the code, I had no way of determining what the name of this new workbook was.  Luckily, Excel opens new workbooks and names them "Book1", "Book2", "Book3", "Book4", etc.  Knowing that these new workbooks would always contain the word "Book" at the beginning, I was able to use a regular expression to identify them.  My regular expression was quite simple, and looked like this: `^Book[0-9]+`.  I have not yet explained what this syntax means, but essentially, we are searching for text that starts with "Book" and ends with 1 or more numbers.  

A more common example for regular expressions is searching large documents for email addresses or phone numbers or even validating user input in a web application.  Chances are, you will not need to use regular expressions on a daily basis, so I am not going to teach you all the nitty gritty details that you will forget within a day.  Instead, I am going to teach you the methodology behind regular expressions that will give you a foundation to work with.  You may have to Google for help regarding specific use cases, but you will never have any confusion about regular expressions.

Let me first start by addressing the fact that there are many different versions of regular expressions.  Here are three different ways to use the same regular expression:

```javascript
// This is how we use a javascript regular expression to match a string with 3 or more digits in it
let myRegExp = /[0-9]{3,}/
let myStringToMatch = '345'

myRegExp.exec(myStringToMatch);  // ["345", index: 0, input: "345", groups: undefined]
```

```python
# This is the same regexp, but in Python

import re

result = re.search('[0-9]{3,}', '345')

print(result.group(0)) # '345'
```

```bash 
# And finally, the same expression written in the bash shell using the grep command

echo "345" | grep -E '[0-9]{3,}' # 345
```

As you can see, all three languages utilize regular expressions a bit differently, but the actual expression that we are writing in each is exactly the same.  Regular expressions are easily translatable from one language to the next.  

The easiest way to explain regular expressions is through practical examples and derivations of _why_ we might need a regular expression for a given scenario.  Let us start with the following text.

```
I am some random text
```

If I wanted to match the word "random" in this text, I could do this with a regular text searching tool.  For example, I could use grep in the following manner.

```bash 
echo "I am some random text" | grep "random"
```

This is trivial and unexciting.  We all understand the basic concept of text matching, but sometimes don't take a moment to think about what it really is.  If we were to write a text matching program, it would roughly follow these steps:

1. Store our search string in a variable 
2. Open our file to search
3. Read each character in the file one by one, seeing if that character matches the first character in our search string
4. If there is a match, advance to the next letter in the search string and check to see if that matches the next character in the file
5. If we reach the end of our search string without any errors, then we have matched the text

This is an overly simplified explanation, but you can [read more here](https://stackoverflow.com/a/1627904/7437737) if you're curious.  What I just explained is called "literal text matching" and can be done using any text searching utility.  It can also be done by a regular expression utility.  If we activate the Perl regular expressions feature of `grep`, we can find this same word.

```bash 
echo "I am some random text" | grep -P "random"
```

If you're wondering how this is any different from my original search, that's good because there is no difference other than the `-P` flag which tells grep to interpret this as a regular expression.  At this point, we have concluded in the most anti-climactic way possible that regular expressions can carry out the basic function of literal text matching.  But this is exactly where regular expressions start to get interesting, because not only can they match literal strings of characters, but also _patterns_ of characters in specified _quantities_.  I will elaborate on this as we move forward, but let's start simple.  Let's say that I had the following file of text called `http-request.txt`.

```
Alt-Svc: quic=":443"; ma=2592000; v="44,43,39"
Cache-Control: private, max-age=0
Content-Encoding: br
Content-Length: 72493
Content-Type: text/html; charset=UTF-8
Date: Mon, 11 Feb 2019 21:40:25 GMT
Expires: -1
Server: gws
Set-Cookie: 1P_JAR=2019-02-11-21; expires=Wed, 13-Mar-2019 21:40:25 GMT; path=/; domain=.google.com
Set-Cookie: SIDCC=AN0-TYtZ7bElYEE0wy8nAaXHUK_GRAsuZzNu7r5OhKVGKwr7a-m7ctz5IIHoZcvmh2s9xuDt0gc; expires=Sun, 12-May-2019 21:40:25 GMT; path=/; domain=.google.com; priority=high
Strict-Transport-Security: max-age=31536000
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block

Alt-Svc: quic=":443"; ma=2592000; v="44,43,39"
Cache-Control: private, max-age=0
Content-Encoding: br
Content-Length: 72470
Content-Type: text/html; charset=UTF-8
Date: Mon, 11 Feb 2019 21:44:38 GMT
Expires: -1
Server: gws
Set-Cookie: 1P_JAR=2019-02-11-21; expires=Wed, 13-Mar-2019 21:44:38 GMT; path=/; domain=.google.com
Set-Cookie: SIDCC=AN0-TYsHoOeMCDEAZfNd9umwLDXDEHqyGfAImuc08v4h2e1B1hSKxGQAq7iVt0xFlQKLzVlgSTM; expires=Sun, 12-May-2019 21:44:38 GMT; path=/; domain=.google.com; priority=high
Strict-Transport-Security: max-age=31536000
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block

Alt-Svc: quic=":443"; ma=2592000; v="44,43,39"
Cache-Control: private, max-age=0
Content-Encoding: br
Content-Length: 72464
Content-Type: text/html; charset=UTF-8
Date: Mon, 11 Feb 2019 21:46:36 GMT
Expires: -1
Server: gws
Set-Cookie: 1P_JAR=2019-02-11-21; expires=Wed, 13-Mar-2019 21:46:36 GMT; path=/; domain=.google.com
Set-Cookie: SIDCC=AN0-TYuz2RnQRkvCL-vKi53aZ9wq43igGogt5iPF1aveuchWK1_5cZsxzom9-PWiJjy8Sk7bvgY; expires=Sun, 12-May-2019 21:46:36 GMT; path=/; domain=.google.com; priority=high
Strict-Transport-Security: max-age=31536000
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block
```

Above are three _separate_ HTTP response headers from three separate requests I made to www.google.com.  As you can see, they all follow a similar data structure, but are not considered "structured" data of any kind.  This is a perfect set of text for us to use to learn regular expressions.  Let's say I wanted to get the date and time of each request in this file.  I could easily find these 3 lines (each request has a date) using a regular expression.

```bash 
cat http-request.txt | grep -P "^Date.+"
```

When executed, this command will find and print out the three date lines.  The "Date" part of the regex makes sense, but what does the ".+" mean?  What is the `^` at the beginning?  If we omit these two characters, we will match the word "Date" 3 times, but we won't get the actual date information that we really want.  This is a perfect opportunity to introduce the "metacharacters".  In regular expressions, the following characters will behave a bit oddly: `. ^ $ * + ? { } [ ] \ | ( )`

If you understand what each of these characters do, you understand how to use regular expressions.  When reading through a file, a regular expression will go line by line (each line indicated by the `\n` character).  When you write a regex, it will be tested against every line of the file.  Knowing this, we can conclude that the "boundary" for a regular expression is just a single line.  In some cases, it may be useful if we had a way of matching text at the beginning or end of a line.  For example, with a list of phone numbers, we might look for a specific area code.

```
234-234-1920
121-726-1382
```

In line 1, the area code is the same as the middle three numbers.  By using the `^` character in our regular expression, we can isolate just the first three characters.

```bash 
cat phone-numbers.txt | grep -P "^234"
```

This regular expression will match just the area code of the first phone number.  Now, let's say that we want to match all the lines of text that end in a question mark.

```
sentences.txt

The regex will not match me.
The regex will not match me either.
But wouldn't it make sense that the regex matched me?
```

Remember, the `?` is a special character, so we must "escape it" using the backslash.

```bash 
cat sentences.txt | grep -P ".+\?$" 
```

This regex will match the entire line that ends in a question mark because we are using the `$` symbol, which represents the end of the line.  This is opposite of the `^` character that we just learned about.

At this point, you've probably already looked up what that period `.` character does in a regex.  When used in a regex, the `.` matches all characters except the newline character (remember, regular expressions use that to determine where the end of a line is?). There are also three other "special" characters that we can use to match certain types of characters.

* `.` - matches any character
* `\d` - matches any digit (0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
* `\s` - matches any whitespace character (including newlines)
* `\w` - matches any alphanumeric character (letters and numbers)

If you capitalize `\D`, `\S`, and `\W`, it negates the expression.  Using this new knowledge, let's try to match the following line of text.

```
You cannot match me because you don't know what a quantifier is!
```

If we tried to match this line using just the skills we know now, we might try something like so: 

```bash
echo "You cannot match me because you don't know what a quantifier is" | grep -P "^Youis$"
```

Shouldn't this work?  We are matching "You" at the beginning of the line (`^`) and "is" at the end of the line (`$`).  The problem is... We failed to match all those words and letters in the middle.  So maybe if we add the `.` in the middle it will match all of them!  Let's try it.

```bash 
echo "You cannot match me because you don't know what a quantifier is" | grep -P "^You.is$"
```

Unfortunately, this isn't going to work either.  The reason this isn't working is because we have not specified _how many_ characters we want to match between the literal word "You" and the literal word "is!".  To do this, we can use either `*`, `+`, `?`, or `{}`.

* `*` - Matches 0 or more of the preceding character
* `+` - Matches 1 or more of the preceding character
* `?` - Matches 0 or 1 of the preceding character
* `{1}` - Matches exactly 1 of the preceding character
* `{1,}` - Matches 1 or more of the preceding character (identical to `+`)
* `{2,6}` - Matches between 2 and 6 of the preceding character

These are what we call "quantifiers", and they are extremely important.  As you might have noticed, you can write _any_ quantifier using the `{}` brackets alone, but sometimes, the `*`, `+`, and `?` are quicker and cleaner to write.  With these quantifiers, we can complete our expression.

```bash 
echo "You can match me now because you know what a quantifier is" | grep -P "^You.+is$"
```

To recap, we are matching "You" at the beginning of the line (`^`), then we are matching 1 or more of _any_ character after that (`.+`), and finally we are matching "is" at the end of the line (`$`).  Below are a few examples that demonstrate the use of quantifiers.

```bash
# Single letter
echo "a" | grep -P "^a" # matches!
echo "a" | grep -P "^a+" # matches!
echo "a" | grep -P "^a*" # matches!
echo "a" | grep -P "^a?" # matches!
echo "a" | grep -P "^a{1}" # matches!
echo "a" | grep -P "^a{1,}" # matches!
echo "a" | grep -P "^a{0,1}" # matches!

# Double letter
echo "aa" | grep -P "^a" # Only matches first letter
echo "aa" | grep -P "^a+" # matches!
echo "aa" | grep -P "^a*" # matches!
echo "aa" | grep -P "^a?" # Only matches the first letter
echo "aa" | grep -P "^a{1}" # Only matches the first letter
echo "aa" | grep -P "^a{1,}" # matches!
echo "aa" | grep -P "^a{0,1}" # Only matches the first letter
echo "aa" | grep -P "^a{0,1}$" # Does not match at all!

# Using metacharacters
echo "a" | grep -P "^\w" # matches!
echo "a" | grep -P "^\w+" # matches!
echo "a" | grep -P "^\w*" # matches!
echo "a" | grep -P "^\w?" # matches!
echo "a" | grep -P "^\w{1}" # matches!
echo "a" | grep -P "^\w{1,}" # matches!
echo "a" | grep -P "^\w{0,1}" # matches!

# Another use of metacharacters (matching anything that is not a digit)
echo "a" | grep -P "^\D" # matches!
echo "a" | grep -P "^\D+" # matches!
echo "a" | grep -P "^\D*" # matches!
echo "a" | grep -P "^\D?" # matches!
echo "a" | grep -P "^\D{1}" # matches!
echo "a" | grep -P "^\D{1,}" # matches!
echo "a" | grep -P "^\D{0,1}" # matches!

# 10 different ways to match the same word
echo "regexp" | grep -P "regexp" # matches!
echo "regexp" | grep -P "^regexp" # matches!
echo "regexp" | grep -P "^reg\w*" # matches!
echo "regexp" | grep -P "^reg\w*$" # matches!
echo "regexp" | grep -P "^\w*$" # matches!
echo "regexp" | grep -P "\w*" # matches!
echo "regexp" | grep -P "^\w+" # matches!
echo "regexp" | grep -P "^regex\w?$" # matches!
echo "regexp" | grep -P "\D{1,}" # matches!
echo "regexp" | grep -P "^\S{1}\w+$" # matches!
```

As you can see in the last couple lines, there are many ways to match the same text.  We could probably find 40 different regular expressions that all match the line "regexp".  And that is not even considering the last metacharacter that we are going to cover!  This entire time, I have not even mentioned "character classes", which are expressions contained within `[]`.  The reason I skipped over these is because when we use them, all the rules change.  The metacharacters (`. ^ $ * + ? { } [ ] \ | ( )`) will all behave differently when placed inside brackets, and furthermore, you can write an adequate regular expression without them 99% of the time!  That said, character classes make your life easier in many cases, so we need to cover them at least briefly.  

You can think of a character class as a single character, but with multiple possibilities.  For example, the following character class represents every lowercase letter in the alphabet, but only 1 of them since we added a quantifier - `[a-z]{1}`.  We could also define only the first 13 letters of the alphabet like so - `[a-m]`.  This extends to digits too.  `[0-9]` represents every possible digit, and is exactly equivalent to `\d`.  `[0-9a-zA-Z_]` represents all alphanumeric characters and is exactly equivalent to `\w`.

You might be wondering why you would ever need something like `[0-9]` when you could just use `\d`, and you are wondering for good reason!  These character classes are not necessary in most cases and I would encourage you to use `\d` rather than `[0-9]` whenever possibly for utmost brevity.  That said, there are certain situations where this could be useful.  Maybe you want to only match numbers 1-5.  There is no abbreviation for the character class `[1-5]` and therefore we must utilize it.

When using character classes, there are a few "gotchas" that we need to cover.  They all relate to the use of metacharacters and how those metacharacters behave in a character class.  In general, I would not recommend trying to use any metacharacter inside a character class (`[]`), but if you do, here are the rules.

* The `^` character does not mean the beginning of a line.  It is a negation symbol.  

```bash
# This expression will match. The first ^ means "beginning of line" while the 
# second ^ (inside the brackets) means "not".  Therefore, this expression
# will match 1 or more characters starting at the beginning of the line that 
# are not digits. 
echo "regexp" | grep -P "^[^0-9]+"
```

* The `.` character matches literally inside brackets

```bash 
# You might think that this will match, but it does not.  This expression matches
# 1 or more period characters starting at the beginning of the line.
echo "regexp" | grep -P "^[.]+"

# This expression does match!
echo "regexp..." | grep -P "^regexp[.]+"
```

Finally, I want to briefly mention why I never talked about the metacharacters `|` and `()`.  These both relate to the topic of "groups", which allows you to group different parts of your regular expression.  If your regular expression is a really long one, it is often helpful to group off different sections of it.  The reason I did not cover this topic is because this topic is far more useful if you are using a programming language like Python for regular expressions because with such a language, you can refer to different groups of your regular expression later in your code.  Since we are learning regular expressions in bash, we generally don't need or have this functionality.

So...

The ultimate conclusion about regular expressions?? 

_There are MANY ways to write them_.

The remainder of this section will walk through a practical example using our newfound regexp skills.  I have attempted to solve the problem two different ways using two different types of regexp syntax to demonstrate that there is more than one way to do things.

### Detailed Example Regular Expression

Let's say we had the following file called `email-addresses.txt`: 

```bash 
jon23@gmail.com
bob879@yahoo.com
not an email
sally2@customsite.com
fred.jones@hotmail.com
not an email address
```

Learning how to match all four of the emails with a single regular expression will demonstrate a lot of the concepts that we have covered.  We start by matching all characters with the `.` metacharacter starting at the beginning of each line (`^`). 

```bash 
cat email-addresses.txt | grep -P "^.+"
```

The expression we just wrote means that we are starting at the beginning of each line and looking for _any_ character except line breaks in a quantity of 1 or more characters.  We could easily have written the same expression differently like so: 

```bash
cat email-addresses.txt | grep -P "^[^\r\n]{1,}"
```

As you can see, regular expressions can be used in a multitude of ways.  In this version, we are doing the _same_ thing we did above with different syntax.  The `^` still indicates that we are looking at the beginning of each line.  The `[^\r\n]` means that we are matching _any_ character that is _not_ (`^`) a carriage return or new line (`\r`, `\n`).  Notice how when we place the `^` inside the character set it now acts as a negation rather than "search from the beginning of the line".  Remember, symbols behave differently when placed inside a character set, so be careful!  Finally, we want to match these characters 1 or more times, so we use the `{1,}` syntax.  The comma after the 1 indicates that we want 1 or _more_ matches.  Anyways, if we run this, we will again match all six lines of the text file.  Since we only want to match the email addresses, we will need to tweak the expression.  Moving forward, I will be writing two regular expressions with different syntax that **both do the same things**.

```bash 
cat email-addresses.txt | grep -P "^[^\r\n]{1,}@.{1,}"
cat email-addresses.txt | grep -P "^.+@.+"
```

The two expressions above both match the four email addresses while excluding the other two lines.  All we had to do was add an "@" symbol followed by the same thing we had before the symbol.  This is great, but what if we modified the text file so it looked like this: 

```
jon23@gmail.com
bob879@yahoo.com
not an email
sally2@customsite.com
this line has an @ symbol in it so it will mess with our regex
fred.jones@hotmail.com
not an email address
```

Now when we run our regular expressions, we will match all the email addresses and the new line that I added.  As you can see, depending on the complexity of the text you are searching, you may have to go through some trial/error before you get the right regular expression for the job.  In this case, we are going to need to modify the back half of the regular expression to the following.

```bash
cat email-addresses.txt | grep -P "^[^\r\n]{1,}@.{1,}\.com"
cat email-addresses.txt | grep -P "^.+@.+\.com"
```

We are now matching just the email addresses again.  All I did was add `\.com` at the end of our regular expression for a _literal_ match (we had to "escape" the period here because otherwise it refers to all characters as it did earlier in the expression.  To escape a special character, we use the backslash right before it).  But what if I modified the text file one more time like so?

```
jon23@gmail.com
bob879@yahoo.com.yahoo.com
not an email
sally2@customsite.net
this line has an @ symbol in it so it will mess with our regex
fred.jones@hotmail.com
not an email address
```

I made two changes here.  First, I made one of the email addresses invalid.  "bob879@yahoo.com.yahoo.com" is obviously an invalid email and we do not want to match it.  Second, "sally2@customsite.net" no longer has ".com" at the end, so this will not match our regexp.  Here is how we would modify the regular expressions to match only the valid email addresses.

```bash 
cat email-addresses.txt | grep -P "^[^\r\n]{1,}@[a-zA-Z0-9]{1,}(.com|.net){1}"
cat email-addresses.txt | grep -P "^.+@\w+(.com|.net){1}"
```

The above regular expressions will get us a lot closer.  In both expressions, we replaced `\.com` with `(.com|.net){1}` to match _either_ ".com" or ".net" email addresses exactly once.  Then, in the first regex, we replaced `.{1,}` with `[a-zA-Z0-9]{1,}` which will now not match the "yahoo.com.yahoo.com" because the periods do not match the character set.  Likewise, we changed the second regular expression from `.+` to `\w+`, which does the same thing.  The only problem we face now is that the regular expressions are still matching the first part of the "bob879@yahoo.com.yahoo.com".  We do not want to match this line _at all_.  To fix this, we modify the regular expressions one more time.

```bash 
cat email-addresses.txt | grep -P "^[^\r\n]{1,}@[a-zA-Z0-9]{1,}(.com|.net){1}$"
cat email-addresses.txt | grep -P "^.+@\w+(.com|.net){1}$"
```

All I did was add the `$` character at the end of each expression.  Just like we have the `^` at the beginning of each expression, we can place the `$` at the end of the expressions to indicate we have reached the end of our line.  This will eliminate that invalid email address!

## Bash Scripting

<iframe width="560" height="315" src="https://www.youtube.com/embed/JdvG_H07CNc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Throughout this tutorial, we have covered many commands and concepts.  Most of the commands we have learned (with the exception of the `awk`) command are for strict usage on the command line, but what if you wanted to put some of these in a script to run?  You could always write out a long and elaborate command and execute it, but it will not get saved and is difficult to write.  Bash scripting solves this problem by allowing you to write common bash commands within a script file and then execute that script file.  You may find this useful if you need to do something on a periodic basis.  For example, maybe you need to clean out a specific folder on your computer every day and place the contents of it in an archived folder with today's date on it.  You can do this through bash scripting, and in this brief introduction, I will teach you how to do just that.  First, we need to understand the basics of a scripting file.

The most basic form of a scripting file is shown below.  This file is called `simple-script.sh` where the `.sh` is the file extension for the script (not necessary, but good practice).  The permissions on this file are `744`, which means that only we (the owner of the script) can modify or execute it.

```bash 
#!/bin/bash

echo "I am a useless, basic script"
```

To run this, we can do so in two ways.

```bash 
bash simple-script.sh 

./simple-script.sh
```

Notice that the top of the file has something called a "shebang" (`#!/bin/bash`), which tells the interpreter which executable to run the script against.  In this case, we are telling it to run with the bash shell which is stored in `/bin` on our machine.  As a note, you generally do not need this shebang as in most cases, your bash shell will execute the scripts by default with bash, but it is good practice and increases portability if you add it.

This is the most basic form of a script, but obviously not all that useful.  Throughout this section, we will learn the most important components of a bash script, which includes: 

* Variable declarations
* Built-in variables
* Command line arguments
* Reading user input
* for loops
* if-then statements

With these concepts, you should be able to accomplish 95% of your tasks.  Sure, there will be times where the above concepts are not enough, but again, this is an introduction to scripting rather than a deep dive.  For the rest of this section, you can assume that I have replaced the contents of a file called `shell-scripting-basics.sh` every time I run a script unless otherwise noted.

### Variable declarations

```bash 
MY_VARIABLE="some value"

echo $MY_VARIABLE
echo "Variables can also be added within double-quoted strings: $MY_VARIABLE"
echo 'But not single-quoted strings. This will not read the variable: $MY_VARIABLE'
```

Declaring and using variables is rather simple in bash scripting, so I will not spend a lot of time here.

### Built-In Variables

There are several built in variables that you can use in a bash script.  They are listed below.

```bash 
echo $0  # Prints the name of the script - shell-scripting-basics.sh
echo $1  # Prints the first argument given to a script
echo $2  # Prints the second argument given to a script 
echo $3  # Prints the third... do I need to continue here??? 
echo $#  # Prints the number of arguments passed to the script
echo $@  # Prints all arguments passed to the script
echo $$  # Prints the process ID
echo $?  # Prints the exit code of the previous process run
```

### Command Line Arguments

A shell script can take arguments on the command line.  For example, if I ran the following script: 

```bash 
#!/bin/bash

echo "The script $0 evaluates to: " $(($1+$2))
```

```bash 
./shell-scripting-basics.sh 3 9

# The script ./shell-scripting-basics.sh evaluates to:  12
```

This script will evaluate to - "The script ./shell-scripting-basics.sh evaluates to:  12"

As you can see, we can use the built-in variables inside our scripts.

### Reading user input

We can also read user input from a script.  This is similar to reading arguments, but instead of the user typing their input in before execution time, they type it in during execution.

```bash
#!/bin/bash

# Reading the user's input into the user_input variable
read user_input

echo "The user entered: $user_input"
```

```bash
./shell-scripting basics
some input
The user entered: some input
```

If you want to protect the user input (ex: password entry), just add an `-s` at the beginning of the command like so: 

```bash 
read -s user_input
```

### for loops 

The syntax for looping in bash is: 

```bash 
#!/bin/bash

for item in $@; do 
        echo $item
done
```

In this script, we are looping through all of the user arguments provided to the script.  Remember, `$@` is a built-in variable containing all of the user arguments.  We can also define an array of variables in bash and loop through them.

```bash 
#!/bin/bash

declare -a my_array=('string 1', 'string 2', 'string 3')

for item in "${my_array[@]}"; do
        echo $item 
done
```

The syntax for an array is a bit weird in bash as you can see.  We can also use the for loop syntax to loop through a bunch of files.  This is a common type of script that you might have to write.

```bash 
#!/bin/bash

# Navigate to the home directory
cd ~/

# The * refers to all the files and directories in the current directory
# This script is basically the `ls` command
for item in *; do
        echo $item
done
```

### if-then statements

I saved if-then statements for last because they can get a bit complicated.  The testing syntax that we use for an if-then statement comes from the `test` command, and you can find all of the possibilities on the man page by typing `man test`.  For most commands in bash, the man pages are difficult to digest and are generally unhelpful for finding quick answers, but the man page for the `test` command is super straightforward and simple.  Therefore, I will not be listing out all of the available testing options and will assume you have read through the man page for `test`.  Below is a simple use of `test` on the command line (outside of a script).

```bash 
test 2 -eq 2; echo $?
# 0

test 2 -eq 3; echo $?
# 1
```

If you run these two commands, you will get output of 0 and 1.  As we talked about, these numbers are the exit codes of the test commands and are stored in the built-in variable `$?`.  Each line shown above is actually two commands.  First, we test a condition, and second, we print the exit code of the previous command (which was test).  If we want to place a test in a script for an if-then statement, we can write it like so: 

```bash 
#!/bin/bash 

if [ 2 -eq 2 ]; then
        echo "2 does equal 2!"
fi
```

This will print "2 does equal 2" because the expression evaluates to true (a 0 exit code).  We can also test other conditions.  For example, we can loop through all the files in our home directory and if they are directories, we will print "$name is a directory" and if not, "$name is a file".

```bash 
#!/bin/bash 

cd ~/

for name in *; do
        if [ -d $name ]; then
                echo "$name is a directory!"
        else
                echo "$name is a file!"
        fi
done
```

The `-d` flag will test whether a given name is a directory.  If it is, it returns true (0 exit code).

Now that you know the basics of bash scripting, we can get to the practical example of checking a specific folder for files and moving them to an archive.  In this case, I will be checking a directory for files that have not been modified for 7 days or more and placing them in an archive folder named with today's date.  This will incorporate the `find` command that we learned earlier!

```bash
#!/bin/bash 

todays_date=$(date +%Y-%m-%d)

# First check if the archives folder exists
if [ ! -d '/home/zach/archives/' ]; then
	mkdir /home/zach/archives/
fi

# Check if today's folder is already created
if [ ! -d "/home/zach/archives/$todays_date" ]; then
	mkdir /home/zach/archives/$todays_date
fi

# A bit of a tricky expression here.  I looked up on StackOverflow how to pipe the output of the find command
# into a do-while loop because the find exec command gets a permission denied error when running it as a script
find /home/zach/folder-to-clean -type f -mtime -7 | 
while read filename
do 
	mv $filename /home/zach/archives/$todays_date/
done
```

### Functions

A programming language is not a language without functions, so here is the basic syntax for writing and then later calling a function in your bash script.

```bash
some_function () {
        
        # If you pass arguments to me, I can print them
        if [ $# -eq 0 ]; then
                # No arguments were passed to function
                echo "I am a function and no arguments were passed to me"
        else
                # At least 1 argument was passed to me, so i will print all of them
                echo "here"
                echo $@
        fi

        # I can return a value that can be retrieved later with the $? built-in variable
        return 20
}

# calling our function
some_function

# calling our function with arguments
some_function "argument 1" "argument 2"

# Printing the return value of 20
echo $?
```

There are endless possibilities to writing bash scripts.  Play around a bit and you will be on your way!

## Virtual Machines and SSH Protocol

<iframe width="560" height="315" src="https://www.youtube.com/embed/wWrQdkoBZEI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

This topic can get quite loaded, and therefore I am not going to dig too deep into it.  We will cover the following: 

* How to setup a public and private keypair for ssh access
* How to use SSH to connect to a remote machine
* How to transfer files between your local machine and remote machine using `scp`
* How to download files from the internet
* How to use VSCode with your VPS

I primarily use Digital Ocean for my hosting needs, so I will be going through a tutorial on how to setup and connect to a virtual private server on Digital Ocean.  That said, the concepts apply across the board whether you are using AWS, Azure, etc.  When you first create a VPS in Digital Ocean (also called a "Droplet"), it will ask you whether you want to connect to the machine using a password or SSH key.  I almost always connect via an SSH key, so let's first learn how to create that key on our local computer.  The process of connecting to a VPS works in the following steps.

1. User creates a private/public SSH keypair on their local computer
2. User inputs the _public_ key into the SSH field of their hosting provider while setting up the host
3. When user tries to connect to the host via SSH, the SSH tool will validate the private/public keypair stored on the local computer in the `~/.ssh` directory with the public key stored on the VPS.
4. If the keys validate, the user now gets remote access to the VPS, and your IP address is stored as a "known host" on the VPS.

So the first step requires us to create a public/private keypair.  We can do this on Mac/Linux using the OpenSSH tool.  In your terminal, type the following command.

```bash 
ssh-keygen
```

This will ask you what directory to store the key in.  For this to work, you need to put it in the `~/.ssh` folder, but you can give it any custom name.  When the command asks you for a password, just press enter twice without entering anything because we do not need to password protect the key since we are using our personal computer.  My new key is stored as `/home/zach/.ssh/id_digitalocean_rsa`.

You now need to print out and copy the public version of this key.  In my case, I type the command: 

```bash 
cat ~/.ssh/id_digitalocean_rsa.pub
```

Notice how I added `.pub` at the end.  When you create a keypair, there will be a `.pub` version always.  Once you have copied the contents of this file, paste it into the SSH key box on your hosting provider.  On Digital Ocean, I will paste it here.

{% asset_img digital-ocean-key.png %}

Once you have done that, you can create your virtual machine.  Now, find the IP address of your new virtual machine and type the following command into your terminal.

```bash 
ssh -p 22 root@157.230.167.2
```

This should successfully log you into your VPS.  Next, I want to show how to transfer files to and from your remote machine and local computer.  To do this, we use the `scp` utility.

### From local computer to remote machine

If I had `sample-file.txt`, the way I would upload this to my remote machine is like so: 

```bash 
scp -r sample-file.txt root@157.230.167.2:~/
```

This will upload the `sample-file.txt` file using the `root` user and place that file in the home directory `~/` on my remote machine.  You can specify any path to place it on your remote machine so long as you put a colon `:` after the IP address.

### From remote machine to local computer

To download that same file from your remote machine to your local computer, you would just run the following.

```bash 
scp -r root@157.230.167.2:~/sample-file.txt ~/Downloads
```

This will place that same sample file into my local computer's `~/Downloads` folder.

### Downloading packages to your remote machine with wget

Sometimes, you will need to download software packages from the internet to your VPS.  Since you do not have a GUI to work with, you must do this with the command line.  Let's say I wanted to download a Google image to my VPS for whatever reason.

Here is a nice picture of a Husky - https://cdn.orvis.com/images/DBS_SibHusky.jpg

We could download this to our VPS using the following command.

```bash 
wget -O my-custom-picture.jpg https://cdn.orvis.com/images/DBS_SibHusky.jpg
```

This will download the photo and save it as `my-custom-picture.jpg` in whatever directory I execute the command from.

### Using VSCode with your remote machine

Sure, you could use the Vim text editor for all of your development needs on a VPS, but it is nice to have a feature-rich text editor like VSCode.  We can use VSCode with files on our VPS with the help of the `rmate` command.  To do this, open VSCode, and download the extension called "Remote VSCode".  Once downloaded, open your Settings by typing ctrl-shift + P, and typing ">Preferences:Open User Settings".  Scroll down and find the "Extensions" dropdown and select "Remote VSCode".  In the settings, you will want the following: 

```
Remote Host: 127.0.0.1
Remote Port: 52698
Remote Onstartup: True (will be a checkbox)
```

Now, type ctrl-shift P again, and type ">Remote: Start Server".  This will start the Remote server.  Now, in your terminal, enter the following command to connect to your VPS.

```bash 
ssh -R 52698:127.0.0.1:52698 root@157.230.167.2
```

Obviously, you will replace the IP address with your own.  Next, install the `rmate` utility on your VPS.

```bash 
sudo wget -O /usr/local/bin/rmate https://raw.github.com/aurora/rmate/master/rmate
sudo chmod a+x /usr/local/bin/rmate
```

You can now edit any file using VSCode by running the following command from your VPS!

```bash 
rmate sample-file.txt
```

## Networking on Command Line

<iframe width="560" height="315" src="https://www.youtube.com/embed/hGjF90P1Hr0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Networking is a huge concept.  There are textbooks written on the topic, and therefore, I have no chance of covering everything you need to know relating to networking.  That said, this section will get into the most common Bash networking utilities that you can use to diagnose network issues on your computer.  If you are completely new to network concepts, that is completely fine as I will be explaining the basics of the internet in the next section.

### Your home network and the internet

To make sense of the commands were are about to run, we need to have at least a foundational understanding of the following.

1. Your Computer's Wireless Card
2. Router
3. Modem
4. What your Internet Service Provider (ISP) is
5. Domain name system (DNS), nameservers, registrars

Sometimes the modem and the router are one and the same, but for our purposes, we will be splitting them into two for a better conceptualization of the topic.  By the end of this section, _you will fully understand (at a high level) what happens when you type "www.thediygolfer.com" in your browser_.  I chose this particular website because I own it.  This will allow us to understand the components that go into it.  It is often difficult to explain these concepts with complex websites like www.google.com because their infrastructure is so complex.  The same concepts apply no matter what website you visit, but keeping things as simple as possible is key to understanding.

_Note: I will be modifying some of the IP addresses and server addresses throughout the tutorial to protect my own privacy, but will not be changing them materially enough to affect the concepts explained herein._

We will start with a diagram to guide us through the process of searching for a website.

{% asset_img basic-web-search.jpg %}

Each piece of the puzzle is labeled by number and illustrates the general flow of information when you make an internet search.  Although in most cases starting at #1 would make sense, we need to understand what an internet service provider (ISP) is and what service it provides.  ISPs are confusing in many cases because they offer more than one service, but in the scope of this post, let's consider an ISP that offers only internet as a service.  An ISP is simply a company that owns hardware (wires) and makes that hardware available to customers in the form of internet service.  What are these wires we speak of?  Many ISPs own a variety of hardware that all are capable of carrying the analog signals we know as the "internet".

* Coaxial cables (what you have in your house)
* Unshielded twisted pair (UTP) cables (100 Mhz)
* Shielded twisted pair (STP) cables
* Fiber optic cables (fastest type - many are in the ocean)

There are multiple ways that internet can reach your home, but the most common is through telephone wires (UTP cables).  Other methods include underground cables and dish satellites.  The method of telephone wires often confuses people because given the name, you would assume that only telephone signals can be sent through telephone wires.  This is not the case.  The internet bootstrapped off of the phone system, and nowadays, there are multiple channels of communication running through each telephone wire.  We call this "broadband" and it is the reason we can talk on the phone, watch Netflix, and search the web all at the same time.  Previously, you had to use "dial up" where you would literally "call in" through the phone line to access the internet.

Regardless the method your ISP uses to get internet to your home, you will always be faced with the same problem.  The signals traveling through the telephone wires your ISP owns are _analog_ signals (wave frequencies), while your computer runs on _digital_ signals (1s and 0s).  This is where the modem comes in.

The modem takes that analog signal, converts it to a digital signal, and sends it over to the router.  We do not yet understand what the router does, but you now have the background information needed to understand my picture diagram.  We know that our internet service provider owns tons of wires and infrastructure that millions of people connect to, and each ISP connects to each other.  This makes up the internet, and is ported into your home through telephone wires, underground cables, or satellite.  When it reaches your home, it is converted to digital signals by your modem and is sent to the router.  Our question now is what does the router do with the signal?

Well, this is a bit of a trick question, because we now must reverse the flow of information and start at the first link in the chain--your computer.  All that "internet connectivity" that your ISP is providing is useless unless you use it, so we first need to make an internet request to see it in action.  As mentioned, we are going to perform a basic search for the homepage of my website, www.thediygolfer.com.

I open my computer, open my browser (in this case, Google Chrome), and type "www.thediygolfer.com" into the search bar.  I press Enter.  At this point, given only one piece of information, my browser application must embark on a journey across the world wide web to find this website.  Remember, a website is simply a bunch of files sitting on a server application that is running on some computer somewhere in the world.  In our case, the website we are searching for is located in New York somewhere, but our browser does not yet know this!

The first step my computer will take is forming a basic HTTP GET request.  It is not important to understand what this means, but understand that it is a structured way that a browser communicates.  Here is my GET request.

```
GET / HTTP/1.1
Host: www.thediygolfer.com
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.96 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
```

I won't explain every line here, but you can reasonably infer what each line means.  The one we will look at is the "Host: www.thediygolfer.com" line.  This information along with the rest is put into a "packet", which is then sent to the router over the connection between the router and your computer's wireless card.  Once it reaches the router, the router will act as a "sorting machine" and route the request where it needs to go.  The real question is... How does the router know where this request needs to go?

This is where the Domain Name System (DNS) comes in.  Across the world, there are thousands of servers that are running for a single purpose.  That purpose is to convert human readable domain names into IP addresses.  In other words, my router knows where to direct my request because it has access to a domain name server somewhere.  Every router will have a default DNS server that it uses.  My router uses the domain name server sitting at `208.67.222.123`.  If you type this IP address into a lookup site like https://whatismyipaddress.com/ip, you will find the following "DNS" information about it.

```
IP:	208.67.222.123
Decimal:	3494108795
Hostname:	resolver1-fs.opendns.com
ASN:	36692
ISP:	OpenDNS, LLC
Organization:	OpenDNS, LLC
Services:	None detected
Type:	Corporate
Assignment:	Static IP
Blacklist:	
Continent:	North America
Country:	United States us flag
Latitude:	37.751  (37° 45′ 3.60″ N)
Longitude:	-97.822  (97° 49′ 19.20″ W)
```

Immediately we can see that this server is run by OpenDNS LLC, which makes sense because this is a well known DNS server.  Another well known DNS server is Google, which runs at the IP address 8.8.8.8.

```
IP:	8.8.8.8
Decimal:	134744072
Hostname:	google-public-dns-a.google.com
ASN:	15169
ISP:	Google
Organization:	Google
Services:	None detected
Type:	Corporate
Assignment:	Static IP
Blacklist:	
Continent:	North America
Country:	United States us flag
Latitude:	37.751  (37° 45′ 3.60″ N)
Longitude:	-97.822  (97° 49′ 19.20″ W)
```

My router will contact its default DNS server, which will then search for the address "www.thediygolfer.com".  Assuming it does not find it right away in its cache, it will start at the root domain database for the `.com` top-level-domain, which is hosted by the company VeriSign.  I know this because [I looked it up on IANA.org](https://www.iana.org/domains/root/db/com.html).  This root database will know where `thediygolfer.com` is because I registered it with an official registrar called [NameSilo](https://www.namesilo.com/register.php?rid=21c9e40dd).  When I registered it, the domain was placed in the `.com` top-level-domain database hosted by Verisign.  I then told NameSilo where I want to point the domain name to.  Since my site is hosted on DigitalOcean, I told NameSilo to point `thediygolfer.com` to DigitalOcean's DNS servers, which are:

```
173.245.58.51
173.245.59.41
198.41.222.173
```

Any of these three servers will know what IP address `thediygolfer.com` maps to.  At this point, my router has started with the default OpenDNS server to look for `thediygolfer.com`, but failing to find it, OpenDNS redirected the router to the root domain database, which then found the domain and redirected the router to Digital Ocean nameservers, which know exactly where the physical machine that my site is running on is.  The router will now figure out the quickest path to this location and send off the request packet.

On the other end, the server which is running my website will find the HTML document that was requested (the home page), package it up, and send it back to the requesting IP address (my computer).  The packets will be delivered to my router, but how does my router know where my computer is?  This brings us to the topic of local area networks, or LANs.  

The router represents a "local network", and actually has a dynamic IP address (DHCP) that will change from time to time.  This is okay because my request I sent has the current IP address for my home network and therefore the server sending back the information will find my network.  Once it finds the network, the router is in charge of routing that information to the correct device in my local network.

A home has multiple devices (laptop, desktop, Chromecast, printer, etc.), and therefore each home network will need multiple IP addresses.  It would be difficult to manage a new IP address for each device out in the wild, but with our local area network, it is simple.  The network itself has an IP address which is called the "default gateway".  This IP address represents all the devices on the network and is where traffic leaves and enters.  Within the local network, each device has a "subnet mask" which will create a unique IP address for each device within the bounds of the local area network address space.  This topic is impossible to understand without an understanding of subnetting, so [I have written a separate post](https://zachgoll.github.io/blog/2019/ip-addresses-netmasks) to explain it for anyone looking to more deeply understand what is going on.  If you choose not to read it, here are the cliffnotes (and a diagram):

{% asset_img ipaddresses.PNG %}

* Your ISP assigns your network an IP address and a subnet mask.  Adding the two together gives you the "network" or "default gateway" address.  In other words, an IP address is made of two parts--the network identifier and the host "address space" identifier.
* The reason we have subnet masks is to conserve the address space used by a single network.  An address space is the range of IP addresses available for devices in a network (i.e. 192.0.168.1, 192.0.168.2, 192.0.168.3, 192.0.168.4, ..., 192.0.168.254)
* DHCP is a server (usually running on a router) that assigns a new device an IP address when it enters a network.  This IP will always be within the address space as defined by the subnet mask.

Anyways, back to our discussion... We have a bunch of data packets coming from the website server and being delivered to our router.  The device you searched from is found by the router, the website data is delivered, and we see the homepage of www.thediygolfer.com.  Although a complex process, it all happens in seconds (or even milliseconds).  With this background knowledge, we can now look at some bash commands to help us diagnose network issues.

### ifconfig

The ifconfig command will give us the information that we just discussed about our LAN (local area network).  This command can also be used to set new configurations, but for our use, we will just look at the output.  Type `ifconfig` in your terminal, and you should get the following output.

```
enp37s0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether 70:85:c2:7c:ff:f2  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

enx000f00de66da: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet <hidden for privacy>  netmask 255.255.255.0  broadcast <hidden for privacy>
        inet6 <hidden for privacy>  prefixlen 64  scopeid 0x20<link>
        ether 00:0f:00:de:66:da  txqueuelen 1000  (Ethernet)
        RX packets 265073  bytes 821138812 (821.1 MB)
        RX errors 0  dropped 1451  overruns 0  frame 0
        TX packets 44132  bytes 102041651 (102.0 MB)
        TX errors 0  dropped 2100 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 752935  bytes 54372769 (54.3 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 752935  bytes 54372769 (54.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

There are three entries in my configuration.  The bottom one called "lo" is a loopback configuration which redirects the address 127.0.0.1 to "localhost", and is commonly used for developing web applications.  The first entry "enp37s0" seems to be an empty configuration.  The middle entry "enx000f00de66da" is what we are interested in, because it displays the IP address of this device, the subnet mask, and the broadcast address of my LAN.  

This is where an [understanding of IP addresses and subnetting on a LAN](https://zachgoll.github.io/blog/2019/ip-addresses-netmasks) is helpful, because the INET address listed is not actually the public IP address recognized by the broader internet.  This IP address is the _local_ identifier which can be translated into the public IP for the network by combining it with the subnet mask which is also listed.  The broadcast address is also listed, but we could easily have derived that from the IP address and the subnet mask as well.

If I typed ifconfig into another computer on my network, the broadcast address and subnet mask will not change, but the IP address will.  There is also data like the maximum transmission units (MTU) which is the maximum size of a packet on this device, and RX/TX packets which indicate how many packets have been transmitted to and from this network.  These values will be constantly increasing.

### ping

The `ping` command is a basic utility you can use to check connectivity between devices on your LAN or even between devices outside your LAN.  This command is useful in cases where you do not have a browser to test internet connectivity.  There are configuration options for the command, but the only one that you need to know is the `-c` option, which will allow you to specify the number of packets to request from a given source.

```bash 
ping -c 5 thediygolfer.com
```

```
PING thediygolfer.com (104.248.115.234) 56(84) bytes of data.
64 bytes from 104.248.115.234 (104.248.115.234): icmp_seq=1 ttl=48 time=36.0 ms
64 bytes from 104.248.115.234 (104.248.115.234): icmp_seq=2 ttl=48 time=49.9 ms
64 bytes from 104.248.115.234 (104.248.115.234): icmp_seq=3 ttl=48 time=35.2 ms
64 bytes from 104.248.115.234 (104.248.115.234): icmp_seq=4 ttl=48 time=34.4 ms
64 bytes from 104.248.115.234 (104.248.115.234): icmp_seq=5 ttl=48 time=35.3 ms

--- thediygolfer.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4005ms
rtt min/avg/max/mdev = 34.427/38.183/49.912/5.885 ms
```

The command above sends my site's homepage 5 separate requests, and we receive data about each request.  Based on the data, we know our computer is online and able to connect to thediygolfer.com.

### traceroute

The traceroute command is a great way to understand how your computer locates and routes your request to www.thediygolfer.com.  I had mentioned earlier that the server for my site is somewhere in New York, and the traceroute command will validate that by showing you the path we take to get there.

This command may not be installed by default on your machine, so if it is not available, you will need to install it.

```bash
# Linux
sudo apt-get update && sudo apt install inetutils-traceroute
```

On Mac, you can access this with the Network Utility.  When we run traceroute, we get the following output.

```bash 
traceroute --resolve-hostnames -q 1 -w 5 -I thediygolfer.com
```

```
traceroute to thediygolfer.com (104.248.115.234), 64 hops max
  1   192.168.0.1 (_gateway)  53.296ms 
  2   142.254.145.21 (142.254.145.21)  10.502ms 
  3   24.164.117.37 (24.164.117.37)  15.953ms 
  4   65.189.128.164 (65.189.128.164)  11.035ms 
  5   65.29.1.87 (be14.pltsohae01r.midwest.rr.com)  18.102ms 
  6   65.29.1.28 (be25.clmkohpe01r.midwest.rr.com)  23.729ms 
  7   66.109.6.68 (bu-ether15.chctilwc00w-bcr00.tbone.rr.com)  31.967ms 
  8   66.109.5.136 (66.109.5.136)  34.155ms 
  9   66.109.5.225 (66.109.5.225)  26.993ms 
 10   64.86.79.97 (ix-ae-27-0.tcore2.ct8-chicago.as6453.net)  25.094ms 
 11   64.86.79.2 (if-ae-22-2.tcore1.ct8-chicago.as6453.net)  33.455ms 
 12   216.6.81.28 (if-ae-26-2.tcore2.nto-new-york.as6453.net)  35.629ms 
 13   66.110.96.5 (if-ae-12-2.tcore1.n75-new-york.as6453.net)  33.047ms 
 14   66.110.96.26 (66.110.96.26)  34.565ms 
 15   * 
 16   * 
 17   104.248.115.234 (104.248.115.234)  35.916ms
 ```

 In the command above, I have indicated to the traceroute utility that I want to resolve IP addresses into their hostnames with the `--resolve-hostnames` command, I want to send only one packet per host `-q 1`, I want to set the timeout for each request to 5 seconds with `-w 5`, and finally, I want to use ICMP protocol rather than UDP with the `-I` flag.  As you can see, the request starts at my computer's gateway, jumps over to a Spectrum server in Kansas, connects to a server in Chicago, connects to Digital Ocean's servers in New York, and finally gets to my website server in New Jersey.  I know the locations because I typed a few of them into [this online tool](https://whatismyipaddress.com/ip-lookup).   

### netstat

This command gives information about the various networking protocols (TCP/IP, UDP, ICMP, etc.) that your machine is using.  Most of what this tool prints is out of the scope of what we discussed above, but I'll mention it nevertheless because it is a crucial tool for understanding how your computer is communicating with the outside world.  For example, we can type the following command to see the activity that is happening on various networking protocols.

```bash 
netstat -s
```

The output of this is below.  Please note that the output has been trimmed for brevity and only the IP, TCP, and UDP protocols have been included.


```bash 
Ip:
    400933 total packets received
    0 forwarded
    0 incoming packets discarded
    400933 incoming packets delivered
    296285 requests sent out
    3 outgoing packets dropped
Tcp:
    32175 active connections openings
    28 passive connection openings
    0 failed connection attempts
    4 connection resets received
    1 connections established
    400885 segments received
    300195 segments send out
    41 segments retransmited
    0 bad segments received.
    7 resets sent
Udp:
    28 packets received
    0 packets to unknown port received.
    0 packet receive errors
    43 packets sent
```

Another useful application of the netstat utility is to see what processes on your computer are using what ports.  We have not discussed processes yet, but keep this in mind for later.

```bash 
netstat -tp
```

```
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 zubuntu:35098           stackoverflow.com:https ESTABLISHED 26679/chrome --type 
```

I have cut off most of the output of this command, but with the single example that I did provide, you can see that I have a Google Chrome window open (with process ID 26679) and one of my tabs is opened to stackoverflow.com.  The local address means that my local host (zubuntu - the fully qualified name for 127.0.0.1) has a socket with ID 35098 open for the given tab in my Google Chrome window.  Each tab will have its own socket.

### dig/host/whois

The `dig`, `host`, and `whois` utilities help find information about domain names, IP addresses, and the mapping between them.  `dig` and `host` should be installed on your machine already, but you may have to install the `whois` utility to use it.  On linux, you can install it by typing `sudo apt-get install whois` in your terminal.

We will start with the `dig` utility which helps us query DNS records for either IP addresses (using a reverse lookup) or a domain name.  Using this utility, we can query my site.

```bash 
dig thediygolfer.com
```

```
; <<>> DiG 9.9.5-3ubuntu0.15-Ubuntu <<>> thediygolfer.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 51716
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;thediygolfer.com.              IN      A

;; ANSWER SECTION:
thediygolfer.com.       600     IN      A       104.248.115.234

;; Query time: 0 msec
;; SERVER: 172.17.0.1#53(172.17.0.1)
;; WHEN: Wed Feb 27 18:41:22 UTC 2019
;; MSG SIZE  rcvd: 61
```

Notice there is a lot of information here, and most of it is comments (indicated by ;;).  To reduce the output that we see, we can add the `+noall` flag and `+answer` flag to reduce all output and only see the answer section. 

```bash 
dig thediygolfer.com +noall +answer
```

```
; <<>> DiG 9.9.5-3ubuntu0.15-Ubuntu <<>> thediygolfer.com +noall +answer
;; global options: +cmd
thediygolfer.com.       600     IN      A       104.248.115.234
```

This will just print the A record for my site in a short format.  We could have also run `dig thediygolfer.com +short` to get a similar output.  But what if we wanted all the DNS records for a domain?  To do this, we can add the `ANY` option.

```bash 
dig thediygolfer.com ANY +noall +answer
```

```
; <<>> DiG 9.9.5-3ubuntu0.15-Ubuntu <<>> thediygolfer.com ANY +noall +answer
;; global options: +cmd
thediygolfer.com.       3599    IN      A       104.248.115.234
thediygolfer.com.       1799    IN      NS      ns1.digitalocean.com.
thediygolfer.com.       1799    IN      NS      ns2.digitalocean.com.
thediygolfer.com.       1799    IN      NS      ns3.digitalocean.com.
thediygolfer.com.       1799    IN      SOA     ns1.digitalocean.com. hostmaster.thediygolfer.com. 1545305910 10800 3600 604800 1800
```

In this response, you can see that I have one A record (the mapping between IP and domain name), three nameserver records, and one "Start of Authority" (SOA) record that indicates Digital Ocean is the authoritative source for the DNS record.

We can also type the following command to find the IP address for a given domain.

```bash 
host thediygolfer.com

# thediygolfer.com has address 104.248.115.234
```

If you use this command with a larger company like Google, you will get more verbose results due to the fact that they operate many servers and many mail servers.

```bash 
host google.com
```

```
google.com has address 108.177.112.139
google.com has address 108.177.112.101
google.com has address 108.177.112.102
google.com has address 108.177.112.100
google.com has address 108.177.112.113
google.com has address 108.177.112.138
google.com has IPv6 address 2607:f8b0:4001:c12::64
google.com mail is handled by 30 alt2.aspmx.l.google.com.
google.com mail is handled by 50 alt4.aspmx.l.google.com.
google.com mail is handled by 40 alt3.aspmx.l.google.com.
google.com mail is handled by 10 aspmx.l.google.com.
google.com mail is handled by 20 alt1.aspmx.l.google.com.
```

Finally, we can use the `whois` command to find more information about a given domain name or IP address.  You may not have this installed by default, so you can type `sudo apt-get install whois` to install it.  Here is an example of how it works.

```bash 
whois thediygolfer
```

```
Domain Name: THEDIYGOLFER.COM
Registry Domain ID: 1896554473_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.namesilo.com
Registrar URL: http://www.namesilo.com
Updated Date: 2018-12-15T15:30:57Z
Creation Date: 2015-01-18T02:22:03Z
Registry Expiry Date: 2020-01-18T02:22:03Z
Registrar: NameSilo, LLC
Registrar IANA ID: 1479
Registrar Abuse Contact Email: abuse@namesilo.com
Registrar Abuse Contact Phone: +1.4805240066
Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
Name Server: NS1.DIGITALOCEAN.COM
Name Server: NS2.DIGITALOCEAN.COM
Name Server: NS3.DIGITALOCEAN.COM
DNSSEC: unsigned
URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/
```

You can see the public information about a given domain.

There are other bash tools like `nslookup`, `route`, etc., but the ones I have reviewed will take care of most user needs.  Unless you are an admin configuring networks on a daily basis, you will never need to use these tools to edit settings.  These commands are useful for quick info relating to your network and external networks.

## Process Management and System Management

<iframe width="560" height="315" src="https://www.youtube.com/embed/lfN2RE8720E" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Process and system management sounds like an intimidating concept, but for the average bash user, there are only a few commands and programs that you will need to learn to get a holistic understanding of how your computer runs.

* What is a process?
* How is a process created?
* How to manage processes with kill, bg, and fg commands
* How to manage processes and monitor system resources with top command

### What is a Process?

Without getting super technical, a process is anything on your computer that is owned by a single user and that consumes one of three system resources.

1. CPU
2. Memory
3. I/O (input/output)

While your computer is on and running, the kernel is constantly working.  The kernel is constantly monitoring all the processes on your computer and allocating one or more of the three system resources to them in intervals.  All the processes are constantly fighting for the resources and will take turns using them.  Believe it or not, when you run a program like Google Chrome, the kernel will give it resources for a few seconds and then do a "context switch" where it hands those same resources off to another process for a few seconds.  If Google Chrome got 100% of the computer's resources 100% of the time, the computer would become dysfunctional because there are many processes that are running at the kernel level to keep everything in order.

### Processes Behind the Scenes

I could list out a bunch of commands here for you, but they will not make any sense unless you understand the sequence that the bash shell (and kernel) takes when a new process is started.  When your computer starts up, the kernel will call a process called "init", which on a UNIX-based operating system is usually the script called `init` located at `/sbin/init`.  This is the [systemd](https://en.wikipedia.org/wiki/Systemd) init script.  This does not make a lot of sense starting out, but once you know how a process starts, you will gain clarity into what goes on behind the scenes on your computer.  A process can start another process (usually the terminal starting a new process as a result of a command typed into it) by first creating a copy of itself, and then executing the new command within that copied process.  Here is a visual to better explain: 

{% asset_img processes-linux.png %}

We can also simulate this process in a terminal ourselves using the `strace()` command.  Please note this command is only available on Linux.  The equivalent command on Mac is the `dtruss` command, but it does not work exactly the same.

```bash
strace ls
```

When you run this command, you will see all of the system calls that were made when you ran the `ls` command.  Although `ls` is considered a "command" in many people's minds, it is really just another process.  I have cut out parts of the `strace ls` output below and highlighted some of the most important parts.

``` 
execve("/bin/ls", ["ls"], [/* 69 vars */]) = 0

.... omitted for brevity ....

write(1, "_config.yml  awk-example.sh  db."..., 107_config.yml  awk-example.sh  db.json      node_modules    package.json  scaffolds  test-permission  yarn.lock
) = 107
write(1, "aapl.csv     data-file.txt   lar"..., 91aapl.csv     data-file.txt   large-data.csv  package-lock.json public       source    themes
) = 91
close(1)                                = 0
munmap(0x7f1578100000, 4096)            = 0
close(2)                                = 0
exit_group(0)                           = ?
+++ exited with 0 +++
```

If you are truly curious about each of the system calls in the `strace` command output, [here is a great StackOverflow post](https://stackoverflow.com/a/6334557).  If you have ever programmed in the C programming language, you might find a few of these commands familiar.  

In the output that I have shown, you can see the `execve` command starts off the process.  This actually does the fork and exec all in one.  Later in the command, you will see file and directory names.  These represent the output of the ls command that you would see if you just ran `ls` in the current directory of my machine.  

In the end, the output is not important to you as a bash user, but is important in trying to understand how the computer starts and ends processes.  What we care more about is how to _manage_ processes.  There are only a few commands that we need to look at here because these few commands will take care of essentially anything we would ever need to do relating to processes.

### Foreground vs. Background Processes

One of the most important concepts to understand regarding processes is the background vs. the foreground process and how to switch between the two.  When you run a process in your bash shell, while that process is running, you will not have access to the terminal.  If you want to stop that process, you can always hit the CTRL-C command.  But there are multiple processes that are running at any given time, so where are they running?  Why don't they prevent you from working in the terminal?  The reason is because they are _background processes_.  We can send a process to the background in one of two ways:

1. Send it to the background at start time
2. Stop it, send it to the background, and start it again

The first method is simple.  We can start a process in the background by adding the `&` character at the end of the command.  For example, I can run the `sleep` command for 20 seconds in the background.

```bash 
sleep 20 &
```

The second method is a bit more complicated and requires us to understand the concept of _process signals_.  We can send process signals using the `kill` command.  You can see all the signals by typing `kill -l`, but here are the most common signals that you might send to a process:

* SIGTERM - `kill` (gracefully sends shutdown signal to process)
* SIGKILL - `kill -9` or `kill -s SIGKILL` (brute force shutdown)
* SIGSTOP - `kill -19` or `kill -s SIGSTOP` (stops running process)
* SIGCONT - `kill -18` or `kill -s SIGCONT` (continues stopped process)
* SIGINT (CTRL-C) - `kill -2` or `kill -s SIGINT` (interrupt process)
* SIGTSTP (CTRL-Z) - `kill -20` or `kill -s SIGTSTP` (stops the running process)

In our case, we want to send the SIGTSTP signal to our running process to throw it in the background and stop it.  We will need the process ID to do this.  To get that process ID, we can run the `ps` command (more on this later).  In my case, I am running the `google-chrome` command (will launch a Google Chrome Window) as a sample process that we can work with, and its process ID is 21124 as shown in the `ps a` output below.

```bash 
  PID TTY      STAT   TIME COMMAND
 1201 tty7     Ssl+   1:48 /usr/lib/xorg/Xorg -core :0 -seat seat0 -auth /var/run/lightdm/root/:0 -nolis
 1204 tty1     Ss+    0:00 /sbin/agetty -o -p -- \u --noclear tty1 linux
17824 pts/0    Ss     0:00 -bash
18747 pts/1    Ss+    0:00 /bin/bash
19664 pts/2    Ss+    0:00 -bash

# This is the Google Chrome Process (this comment was manually added to ps output)
21124 pts/2    SLl    0:03 /opt/google/chrome/chrome

21129 pts/2    S      0:00 cat
21130 pts/2    S      0:00 cat
21133 pts/2    S      0:00 /opt/google/chrome/chrome --type=zygote --enable-crash-reporter=2475ab0f-df4d
21134 pts/2    S      0:00 /opt/google/chrome/nacl_helper
21137 pts/2    S      0:00 /opt/google/chrome/chrome --type=zygote --enable-crash-reporter=2475ab0f-df4d
21164 pts/2    Sl     0:00 /opt/google/chrome/chrome --type=gpu-process --field-trial-handle=39017447716
21169 pts/2    SLl    0:00 /opt/google/chrome/chrome --type=utility --field-trial-handle=390174477165224
21309 pts/2    Sl     0:00 /opt/google/chrome/chrome --type=renderer --field-trial-handle=39017447716522
21360 pts/2    Sl     0:01 /opt/google/chrome/chrome --type=renderer --field-trial-handle=39017447716522
21398 pts/2    Sl     0:00 /opt/google/chrome/chrome --type=renderer --field-trial-handle=39017447716522
21417 pts/2    Sl     0:00 /opt/google/chrome/chrome --type=renderer --field-trial-handle=39017447716522
21711 pts/2    Sl     0:01 /opt/google/chrome/chrome --type=renderer --field-trial-handle=39017447716522
21751 pts/2    Sl     0:00 /opt/google/chrome/chrome --type=renderer --field-trial-handle=39017447716522
21852 pts/2    Sl     0:00 /opt/google/chrome/chrome --type=renderer --field-trial-handle=39017447716522
21870 pts/0    R+     0:00 ps a
```

I actually have several options here to stop the running Google Chrome process.  I could send the `SIGTSTP` signal (i.e. CTRL-Z), or I could send the `SIGSTOP` process signal to the process.  Either of these will stop the running process and give us our terminal back.  I am going to send the `SIGSTOP` process from a separate terminal window.

```bash 
kill -s SIGSTOP 21124
# `kill -19 21124` also works
```

You will notice that when you try to do anything in the Chrome window, it will not work because it is a stopped process.  We can now start the process again but this time, we will throw it in the background.  To do this, go to the terminal where Google Chrome has stopped and type the command `jobs`.  This will give you that terminal's list of jobs.  We want to find the number that Google Chrome is identified by (in my case it is job #1) and run the following command.

```bash 
bg %1
```

Google Chrome is now started again and in the background.  Let's now stop it again, start it again, and then bring it back to the foreground.

```bash 
# Stops process
kill -s SIGSTOP 211124

# Starts process
kill -s SIGCONT 211124

# Brings process to foreground
fg %1
```

### ps and top commands (system performance management)

There are two commands that give us output relating to the processes that are currently running on our computer.  In 99% of the cases, these commands can be run interchangeably for the same type of data.  The difference between the commands is the level of interactivity and therefore the situations you would be likely to use them both in.  For example, when you run the `top` command, you will see a bunch of processes listed out.  Not only will this command list processes, but it will also update the status of each process in real time.  Since it is interactive, other scripts cannot use it to get information about processes without special configurations (i.e. using "batch" mode).  That is where the `ps` command enters the picture.  This command can be run inside bash scripts to obtain necessary information.  Since we are not looking to write complex process utilization scripts, we will be primarily focusing on the `top` command because it is more user-friendly.  If you want, you can read the man pages for the `ps` command to learn the equivalent commands that we talk about (hint: `ps ax` will print out all your current processes).

Our first priority is to learn the interface of the `top` command and what we need to look for in the output.  This is not just a bash command but an entire program that has extensive capabilities.  As a normal bash user you will not use most of the capabilities, but we will look at some useful and common ones.

{% asset_img top-header.png %}

When first starting the top program, you will see the above output in the header.  There will be 5 lines here, and starting from the top line (1) going to the bottom line (5), let's get a better idea of what this means.

1. Not all that useful to us--just general info
2. Gives a breakout of the different types of tasks running on your computer.  These can be running, stopped, sleeping, or zombie.  These states are intuitive, except a zombie process, which is just a process that is killed but still showing up in the process table (usually because the parent process has failed to clean it up).
3. CPU time statistics including us (user time un-niced), sy (system time), ni (user time niced), id (idle time), and wa (I/O wait time).  The hi, si, and st are not important to us.  We can find the total user time (time it takes to run the actual code of a program) by adding us + ni.  This line will be more helpful to us when we are filtered by a specific process later.
4. Actual memory statistics.  If you are displaying all your processes and your free memory is low, this could be a red flag that you are running out of RAM.
5. Swap memory statistics.  Swap memory is only used when real memory is exhausted, so if you start seeing low numbers in the "free" part of _real memory_ and high numbers in the "used" part of _swap memory_, there could be a performance issue on your computer caused by too little RAM.

Moving down to the actual output of the top command, you may notice that it updates every few seconds.  This is useful because we get to see our computer in real time.  That said, it would be nice if we could control the interval length.  We can do this in one of two ways, which is a good introduction to the topic of "interactive" mode vs. "static" mode.  In static mode, we set configuration of the top program on the command line.  For example, we might open the top program like so: 

```bash 
top -d 10
```

This will open the top program and set the interval to 10 seconds.  We could also adjust this value by typing the letter `d` within the top program, and entering the number of seconds for the interval.  How did I know this?  For one, I read the man pages for the top command, but another way to find this info is by typing the `?` within the top program.  This will drop you into a help page that gives you hints on how to use the program.  From here you will only get information about the "interactive" mode, but generally, this is all you will ever need.  Unlike the `ps` command, we will never need to re-type the `top` command because all our configurations can be done within the program itself.

The top command is great because it acts as a process management tool and system management tool all in one.  Continuing our discussion on process management, we can kill processes within the top program.  Just type the letter `k` followed by the process ID.  This will send the default `SIGINT` kill signal to the process.  This does not have any customization (i.e. you cannot send a custom signal), but is a quick way to kill a process without breaking out of top.

We can also filter the top program by process ID, or even user ID.  Both of these can be set at the command line when starting the program.

```bash
# Only displays the user "zach"'s processes
top -u zach

# Only displays the process 22435
top -p 22435
```

We can also do these commands interactively within the top program.  If we wanted to only show the user `zach` processes, we type the letter `n` followed by "zach".  If we wanted to display only process ID `22435`, we could put a filter on the top output.  We do this like so: 

1. Type the letter `O` (uppercase) in interactive mode
2. Type `PID=22435` and press Enter
3. Check your filters by typing CTRL-o (control + the letter o)
4. Clear your filters by typing `=`

By default, top gives quite a bit of output, and oftentimes, we will need to scroll through it.  You can scroll through the top output using the up, down, left, and right keys.  To see what coordinates you are at in the output, just type `C`.  This will give you an output at the top of your screen that says something like `scroll coordinates: y = 13/345 (tasks), x = 1/12 (fields)`.

We can also change what fields are displayed by typing the letter `f` in interactive mode.  When you type this, you will see a bunch of different field options.  Before we go into how we can organize our fields, we need to walk through the default fields and what they mean.

1. PID - process ID
2. USER - user ID that owns the process
3. PR - The priority of the process (`rt` means the priority is set in real time)
4. NI - The "Nice" value of the process.  This is the user space version of priority and is directly related to priority (PR) by the following formula: PR = 20 + NI.  For both, the lower the number, the higher the priority.  See [this post](https://askubuntu.com/a/656787/917201) for a more detailed explanation.
5. VIRT - All virtual memory used for the process
6. RES - subset of VIRT
7. SHR - subset of RES
8. S - state (S for sleep, R for running, I for idle)
9. %CPU - The task's share of elapsed CPU time since last screen update.  So if it is 50% and your interval time is 10 seconds, this means that over the last 10 seconds, this process has taken up 50% of all available CPU time.
10. %MEM - The same thing as RES, but expressed as a percentage
11. TIME+ - The total time the process has used since start time (assuming cumulative mode is on - toggle it with `S`)
12. COMMAND - The command that started the program.  Type `c` to toggle between the full name and abbreviated name.

Unless you are a system management wizard, some of these default fields are not going to be that useful to you.  To change what fields are displayed, you can type `f` to enter the field manager.  When in the field manager, use you up and down arrow keys to highlight a specific field.  If the field has a `*` by it, this means that it is currently showing.  You can toggle this on and off using the letter `d`.  To move commands to a different location in the menu, highlight one, press the right arrow key to highlight the entire field, and then press up and down arrow keys until you have placed it where you want it.  Finally, press the left arrow key or Enter to place the menu item.  You can also change the field which top sorts by highlighting the field and typing the letter `s`.  Press `q` to quit.  Once you have set your favorite configuration, exit out of the field manager and type `W` to save your settings to the `~/.toprc` file.

If you want to display multiple windows in top (usually I do not need this feature), you can enter alternate display mode by typing `A`.  Once in this mode, you can use the `a` and `w` keys to move between the four windows (you will see the window update in the top left corner of the page), and type `G` to rename the current window to whatever you want.  The advantage here is the ability to see four windows that are all configured slightly different at the same time.

So we know how to use the top command, but what does it help us with?  Generally, you would use this program to check just a few things: 

1. The summary page - for high level statistics about your computer
2. %CPU - check to make sure a single process is not eating all of your CPU resources
3. %MEM - check to make sure that a single process is not eating all of your memory resources.  Remember, the %MEM represents `RES`, which is "resident memory", or in other words, "real memory".  VIRT = RES + SWAP, so if a task's VIRT is much higher than RES, this means that it is using SWAP, which means that the computer is running  out of space for the process.

The top command is great for getting a high level overview of things in regards to process/system utilization, but we have a few more commands that will give greater insight into how our computer is functioning.

### lsof 

The lsof command is used for listing out open files on the system.  At first glance it does not sound all that interesting, but since UNIX based operating systems store everything as a file, this tool can allow us to see more than just open text files.  There are many ways to utilize this tool, but here are a couple that any bash user might find useful at some point.

```bash 
# List all the files that the user zach has open
lsof -u zach

# List all network connections
lsof -i

# List all processes running on port 22
lsof -i TCP:22

# List all the files that my external hard drive has open 
# The +f -- means that we are treating the following as a mount point
lsof +f -- /media/my_hard_drive
```

### free, time

The free command is a concise way of showing your system resources at a given moment.  I suggest appending the `--mega` flag to display the amount of memory in megabytes rather than the default kibibytes.

```bash 
free --mega
```

There is also a command called `time` that will tell you how much CPU time a particular command takes.  For example, I could run the following command: 

```bash 
time google-chrome

# Output
# ---------------
# real	0m0.458s
# user	0m0.229s
# sys	0m0.063s
```

This will run the `google-chrome` executable (opens a Google Chrome window) and track how much real, user, and system time was used for that executable.  Real time is the total elapsed time that it took to run the program.  User time is the time it took the code to run, and System time is the time which the kernel was using system resources.  The following formula will determine how long the system was waiting for resources.

Wait Time = Real - User - System

It is difficult to know what a reasonable wait time is without benchmarking, but this would be the tool to use for such an analysis.

## Conclusion 

You may have never thought this would happen, but the post is finally over.  By now, you should have an intermediate to advanced skill-set using the bash shell, and paired with [users, groups, and permissions](https://zachgoll.github.io/blog/2019/user-permissions/), you will have a high level understanding of Bash (and even Linux for that matter).