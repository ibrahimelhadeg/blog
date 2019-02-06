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
* awk/sed
* less
* find
* exec
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
2018-11-09,205.550003,206.009995,202.250000,204.470001,204.470001,34365800
2018-11-12,199.000000,199.850006,193.789993,194.169998,194.169998,51135500
2018-11-13,191.630005,197.179993,191.449997,192.229996,192.229996,46882900
2018-11-14,193.899994,194.479996,185.929993,186.800003,186.800003,60801000
2018-11-15,188.389999,191.970001,186.899994,191.410004,191.410004,46478800
2018-11-16,190.500000,194.970001,189.460007,193.529999,193.529999,36928300
2018-11-19,190.000000,190.699997,184.990005,185.860001,185.860001,41925300
2018-11-20,178.369995,181.470001,175.509995,176.979996,176.979996,67825200
2018-11-21,179.729996,180.270004,176.550003,176.779999,176.779999,31124200
2018-11-23,174.940002,176.600006,172.100006,172.289993,172.289993,23624000
2018-11-26,174.240005,174.949997,170.259995,174.619995,174.619995,44998500
2018-11-27,171.509995,174.770004,170.880005,174.240005,174.240005,41387400
2018-11-28,176.729996,181.289993,174.929993,180.940002,180.940002,46062500
2018-11-29,182.660004,182.800003,177.699997,179.550003,179.550003,41770000
2018-11-30,180.289993,180.330002,177.029999,178.580002,178.580002,39531500
2018-12-03,184.460007,184.940002,181.210007,184.820007,184.820007,40802500
2018-12-04,180.949997,182.389999,176.270004,176.690002,176.690002,41344300
2018-12-06,171.759995,174.779999,170.419998,174.720001,174.720001,43098400
2018-12-07,173.490005,174.490005,168.300003,168.490005,168.490005,42281600
2018-12-10,165.000000,170.089996,163.330002,169.600006,169.600006,62026000
2018-12-11,171.660004,171.789993,167.000000,168.630005,168.630005,47281700
2018-12-12,170.399994,171.919998,169.020004,169.100006,169.100006,35627700
2018-12-13,170.490005,172.570007,169.550003,170.949997,170.949997,31898600
2018-12-14,169.000000,169.080002,165.279999,165.479996,165.479996,40703700
2018-12-17,165.449997,168.350006,162.729996,163.940002,163.940002,44287900
2018-12-18,165.380005,167.529999,164.389999,166.070007,166.070007,33841500
2018-12-19,166.000000,167.449997,159.089996,160.889999,160.889999,49047300
2018-12-20,160.399994,162.110001,155.300003,156.830002,156.830002,64773000
2018-12-21,156.860001,158.160004,149.630005,150.729996,150.729996,95744600
2018-12-24,148.149994,151.550003,146.589996,146.830002,146.830002,37169200
2018-12-26,148.300003,157.229996,146.720001,157.169998,157.169998,58582500
2018-12-27,155.839996,156.770004,150.070007,156.149994,156.149994,53117100
2018-12-28,157.500000,158.520004,154.550003,156.229996,156.229996,42291400
2018-12-31,158.529999,159.360001,156.479996,157.740005,157.740005,35003500
2019-01-02,154.889999,158.850006,154.229996,157.919998,157.919998,37039700
2019-01-03,143.979996,145.720001,142.000000,142.190002,142.190002,91312200
2019-01-04,144.529999,148.550003,143.800003,148.259995,148.259995,58607100
2019-01-07,148.699997,148.830002,145.899994,147.929993,147.929993,54777800
2019-01-08,149.559998,151.820007,148.520004,150.750000,150.750000,41025300
2019-01-09,151.289993,154.529999,149.630005,153.309998,153.309998,45099100
2019-01-10,152.500000,153.970001,150.860001,153.800003,153.800003,35780700
2019-01-11,152.880005,153.699997,151.509995,152.289993,152.289993,27023200
2019-01-14,150.850006,151.270004,149.220001,150.000000,150.000000,32439200
2019-01-15,150.270004,153.389999,150.050003,153.070007,153.070007,28710900
2019-01-16,153.080002,155.880005,153.000000,154.940002,154.940002,30569700
2019-01-17,154.199997,157.660004,153.259995,155.860001,155.860001,29821200
2019-01-18,157.500000,157.880005,155.979996,156.820007,156.820007,33751000
2019-01-22,156.410004,156.729996,152.619995,153.300003,153.300003,30394000
2019-01-23,154.149994,155.139999,151.699997,153.919998,153.919998,23130600
2019-01-24,154.110001,154.479996,151.740005,152.699997,152.699997,25441500
2019-01-25,155.479996,158.130005,154.320007,157.759995,157.759995,33535500
2019-01-28,155.789993,156.330002,153.660004,156.300003,156.300003,26192100
2019-01-29,156.250000,158.130005,154.110001,154.679993,154.679993,41587200
2019-01-30,163.250000,166.149994,160.229996,165.250000,165.250000,61109800
2019-01-31,166.110001,169.000000,164.559998,166.440002,166.440002,40739600
2019-02-01,166.960007,168.979996,165.929993,166.520004,166.520004,32668100
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
2018-11-09 205.550003 206.009995 202.250000 204.470001 204.470001 34365800
2018-11-12 199.000000 199.850006 193.789993 194.169998 194.169998 51135500
2018-11-13 191.630005 197.179993 191.449997 192.229996 192.229996 46882900
2018-11-14 193.899994 194.479996 185.929993 186.800003 186.800003 60801000
2018-11-15 188.389999 191.970001 186.899994 191.410004 191.410004 46478800
2018-11-16 190.500000 194.970001 189.460007 193.529999 193.529999 36928300
2018-11-19 190.000000 190.699997 184.990005 185.860001 185.860001 41925300
2018-11-20 178.369995 181.470001 175.509995 176.979996 176.979996 67825200
2018-11-21 179.729996 180.270004 176.550003 176.779999 176.779999 31124200
2018-11-23 174.940002 176.600006 172.100006 172.289993 172.289993 23624000
2018-11-26 174.240005 174.949997 170.259995 174.619995 174.619995 44998500
2018-11-27 171.509995 174.770004 170.880005 174.240005 174.240005 41387400
2018-11-28 176.729996 181.289993 174.929993 180.940002 180.940002 46062500
2018-11-29 182.660004 182.800003 177.699997 179.550003 179.550003 41770000
2018-11-30 180.289993 180.330002 177.029999 178.580002 178.580002 39531500
2018-12-03 184.460007 184.940002 181.210007 184.820007 184.820007 40802500
2018-12-04 180.949997 182.389999 176.270004 176.690002 176.690002 41344300
2018-12-06 171.759995 174.779999 170.419998 174.720001 174.720001 43098400
2018-12-07 173.490005 174.490005 168.300003 168.490005 168.490005 42281600
2018-12-10 165.000000 170.089996 163.330002 169.600006 169.600006 62026000
2018-12-11 171.660004 171.789993 167.000000 168.630005 168.630005 47281700
2018-12-12 170.399994 171.919998 169.020004 169.100006 169.100006 35627700
2018-12-13 170.490005 172.570007 169.550003 170.949997 170.949997 31898600
2018-12-14 169.000000 169.080002 165.279999 165.479996 165.479996 40703700
2018-12-17 165.449997 168.350006 162.729996 163.940002 163.940002 44287900
2018-12-18 165.380005 167.529999 164.389999 166.070007 166.070007 33841500
2018-12-19 166.000000 167.449997 159.089996 160.889999 160.889999 49047300
2018-12-20 160.399994 162.110001 155.300003 156.830002 156.830002 64773000
2018-12-21 156.860001 158.160004 149.630005 150.729996 150.729996 95744600
2018-12-24 148.149994 151.550003 146.589996 146.830002 146.830002 37169200
2018-12-26 148.300003 157.229996 146.720001 157.169998 157.169998 58582500
2018-12-27 155.839996 156.770004 150.070007 156.149994 156.149994 53117100
2018-12-28 157.500000 158.520004 154.550003 156.229996 156.229996 42291400
2018-12-31 158.529999 159.360001 156.479996 157.740005 157.740005 35003500
2019-01-02 154.889999 158.850006 154.229996 157.919998 157.919998 37039700
2019-01-03 143.979996 145.720001 142.000000 142.190002 142.190002 91312200
2019-01-04 144.529999 148.550003 143.800003 148.259995 148.259995 58607100
2019-01-07 148.699997 148.830002 145.899994 147.929993 147.929993 54777800
2019-01-08 149.559998 151.820007 148.520004 150.750000 150.750000 41025300
2019-01-09 151.289993 154.529999 149.630005 153.309998 153.309998 45099100
2019-01-10 152.500000 153.970001 150.860001 153.800003 153.800003 35780700
2019-01-11 152.880005 153.699997 151.509995 152.289993 152.289993 27023200
2019-01-14 150.850006 151.270004 149.220001 150.000000 150.000000 32439200
2019-01-15 150.270004 153.389999 150.050003 153.070007 153.070007 28710900
2019-01-16 153.080002 155.880005 153.000000 154.940002 154.940002 30569700
2019-01-17 154.199997 157.660004 153.259995 155.860001 155.860001 29821200
2019-01-18 157.500000 157.880005 155.979996 156.820007 156.820007 33751000
2019-01-22 156.410004 156.729996 152.619995 153.300003 153.300003 30394000
2019-01-23 154.149994 155.139999 151.699997 153.919998 153.919998 23130600
2019-01-24 154.110001 154.479996 151.740005 152.699997 152.699997 25441500
2019-01-25 155.479996 158.130005 154.320007 157.759995 157.759995 33535500
2019-01-28 155.789993 156.330002 153.660004 156.300003 156.300003 26192100
2019-01-29 156.250000 158.130005 154.110001 154.679993 154.679993 41587200
2019-01-30 163.250000 166.149994 160.229996 165.250000 165.250000 61109800
2019-01-31 166.110001 169.000000 164.559998 166.440002 166.440002 40739600
2019-02-01 166.960007 168.979996 165.929993 166.520004 166.520004 32668100
2019-02-04 167.410004 171.660004 167.279999 171.250000 171.250000 31495500
2019-02-05 172.860001 175.080002 172.350006 174.179993 174.179993 36066500
```

This will print the output of the sed transformation.  If I add the -i flag to the command, it will edit the file.

```bash
sed -i 's/,/ /g' AAPL.csv
```

Now that we have the file separated by spaces, we can look to `awk` to do some better summarization.  This data is a bit confusing in its current state because it has multiple versions of the stock price (open, high, low, close, adjusted).  I just want to see the date, open, and volume.  To get that, I can use awk.

## Advanced Bash 

Not all of the following topics are solely bash concepts, but they are important to be aware of even if you are not going to be using them on a daily basis.  The breadth of concepts go to show the true power of the bash shell.  The topics are as follows.

* Regular expressions (used with scripting)
* Bash Scripting
* Virtual Machines and SSH 
* Networking on the command line
* Process management
* System Management