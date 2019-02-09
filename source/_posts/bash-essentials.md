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

```bash 
awk -F " " ' BEGIN { print "Date\t\tPrice\t\tVolume" }; NR > 1 { print $1 "\t" $2 "\t" $7 } ' aapl.csv
```

```
Date            Price           Volume
2018-11-06      201.919998      31882900
2018-11-07      205.970001      33424400
2018-11-08      209.979996      25362600
2018-11-09      205.550003      34365800
2018-11-12      199.000000      51135500
2018-11-13      191.630005      46882900
2018-11-14      193.899994      60801000
2018-11-15      188.389999      46478800
2018-11-16      190.500000      36928300
2018-11-19      190.000000      41925300
2018-11-20      178.369995      67825200
2018-11-21      179.729996      31124200
2018-11-23      174.940002      23624000
2018-11-26      174.240005      44998500
2018-11-27      171.509995      41387400
2018-11-28      176.729996      46062500
2018-11-29      182.660004      41770000
2018-11-30      180.289993      39531500
2018-12-03      184.460007      40802500
2018-12-04      180.949997      41344300
2018-12-06      171.759995      43098400
2018-12-07      173.490005      42281600
2018-12-10      165.000000      62026000
2018-12-11      171.660004      47281700
2018-12-12      170.399994      35627700
2018-12-13      170.490005      31898600
2018-12-14      169.000000      40703700
2018-12-17      165.449997      44287900
2018-12-18      165.380005      33841500
2018-12-19      166.000000      49047300
2018-12-20      160.399994      64773000
2018-12-21      156.860001      95744600
2018-12-24      148.149994      37169200
2018-12-26      148.300003      58582500
2018-12-27      155.839996      53117100
2018-12-28      157.500000      42291400
2018-12-31      158.529999      35003500
2019-01-02      154.889999      37039700
2019-01-03      143.979996      91312200
2019-01-04      144.529999      58607100
2019-01-07      148.699997      54777800
2019-01-08      149.559998      41025300
2019-01-09      151.289993      45099100
2019-01-10      152.500000      35780700
2019-01-11      152.880005      27023200
2019-01-14      150.850006      32439200
2019-01-15      150.270004      28710900
2019-01-16      153.080002      30569700
2019-01-17      154.199997      29821200
2019-01-18      157.500000      33751000
2019-01-22      156.410004      30394000
2019-01-23      154.149994      23130600
2019-01-24      154.110001      25441500
2019-01-25      155.479996      33535500
2019-01-28      155.789993      26192100
2019-01-29      156.250000      41587200
2019-01-30      163.250000      61109800
2019-01-31      166.110001      40739600
2019-02-01      166.960007      32668100
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
# c is create mode, v is verbo#!/bin/bash 

todays_date=$(date +%Y-%m-%d)

# First check if the archives folder exists
if [ ! -d '/home/zach/archives/' ]; then
	mkdir /home/zach/archives/
fi

# Check if today's folder is already created
if [ ! -d "/home/zach/archives/$todays_date" ]; then
	mkdir /home/zach/archives/$todays_date
fi

find /home/zach/folder-to-clean -type f -mtime -7 | 
while read filename
do 
	mv $filename /home/zach/archives/$todays_date/
done output, f is tar format
tar cvf archive.tar file1 file#!/bin/bash 

todays_date=$(date +%Y-%m-%d)

# First check if the archives folder exists
if [ ! -d '/home/zach/archives/' ]; then
	mkdir /home/zach/archives/
fi

# Check if today's folder is already created
if [ ! -d "/home/zach/archives/$todays_date" ]; then
	mkdir /home/zach/archives/$todays_date
fi

find /home/zach/folder-to-clean -type f -mtime -7 | 
while read filename
do 
	mv $filename /home/zach/archives/$todays_date/
donefile3 ... filen

# List what is in .tar archive#!/bin/bash 

todays_date=$(date +%Y-%m-%d)

# First check if the archives folder exists
if [ ! -d '/home/zach/archives/' ]; then
	mkdir /home/zach/archives/
fi

# Check if today's folder is already created
if [ ! -d "/home/zach/archives/$todays_date" ]; then
	mkdir /home/zach/archives/$todays_date
fi

find /home/zach/folder-to-clean -type f -mtime -7 | 
while read filename
do 
	mv $filename /home/zach/archives/$todays_date/
done
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

## Advanced Bash 

Not all of the following topics are solely bash concepts, but they are important to be aware of even if you are not going to be using them on a daily basis.  The breadth of concepts go to show the true power of the bash shell.  The topics are as follows.

* Regular expressions (used with scripting)
* Bash Scripting
* Virtual Machines and SSH 
* Networking on the command line
* Process management
* System Management

### Regular Expressions

To me, regular expressions are often made far more complicated than they need to be. Sure, there are a lot of options and little details to learn regarding regular expressions, and on top of that, there are many different flavors of regular expressions (python, extended, rust, etc.).  Despite this, there are only a few core concepts that one must understand about regular expressions that will then translate in the ability to use any flavor of regular expressions effectively.

Regular expressions exist because a literal text searching program is sometimes not good enough.  Let me give you an extremely practical example from my own work to explain.

I recently wrote a script in Microsoft Excel VBA that executed commands from an external library.  The code of this external library was not available for me to see, and therefore, I had to use it with limited control.  As a result of this, the library would open up a new Excel workbook for every function call I made.  In each workbook, there was data that I needed to copy and paste into my main workbook, but in the code, I had no way of determining what the name of this new workbook was.  Luckily, Excel opens new workbooks and names them "Book1", "Book2", "Book3", "Book4", etc.  Knowing that these new workbooks would always contain the word "Book" at the beginning, I was able to use a regular expression to identify them.  My regular expression was quite simple, and looked like this: `^Book[1-9]+`.  I have not yet explained what this syntax means, but essentially, we are searching for text that starts with "Book" and ends with 1 or more numbers.  

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

There are only 4 concepts that you must learn with regular expressions:

1. Character sets
2. Sub-expressions
3. Quantifiers
4. Literals

Of course, we will be learning regular expressions through the lens of the bash shell, and therefore we will need to use the `grep` command using the `-P` flag, which stands for "perl regular expressions".  This is the same flavor of regular expression that Python uses, so I thought it would be convenient to teach it.  If you are running a Mac, you may have to install the GNU version of the `grep` utility with Homebrew to enable the `-P` flag.  Below is a regular expression with each of the 3 concepts displayed.

```bash 
echo "The 1st phone number is: 111-222-3333" | grep -P "(The 1st phone number is: ){1}[0-9]{3}-[0-9]{3}-[0-9]{4}"
```

Let's break this down.  The `(The 1st phone number is: )` is an expression, and when we have an expression like this, we can use it to match an entire group of words.  The `{1}` immediately after it means that we want to match this entire phrase exactly 1 time.  It is what we call a "quantifier".  The rest of the regular expression is `[0-9]` followed by `{3}` and a literal `-` character.  The `[0-9]` is a character set and it means that we are looking for any number in a quantity of exactly 3 `{3}` or exactly 4 `{4}`.  So to review:

* A character set is anything in brackets.  We can put 
* A sub-expression is anything in parenthesis
* A quantifier is anything in braces (i.e. `{}`)
* A literal character is anything that is not in brackets, parentheses, or braces

It would be overly tedious for me to list out all the possible things you can put in each of these, so I will go through an example of each.

#### Character Set

A character set is where we can define a "range" of characters that we want to match.  If we wanted to match any number, we could use the character set `[0-9]`.  If we wanted to match any letter, we could use the character set `[a-zA-Z]`.  If we wanted to match letters and numbers, we could use `[0-9a-zA-Z]`.

#### Sub-expressions

A sub-expression is how we can "chunk" characters together into a single phrase.  Maybe we wanted to match a bunch of emails that are going to be either "gmail.com" or "yahoo.com".  We could do this using a sub-expression: `(gmail.com|yahoo.com)`.  The `|` means "or". 

#### Quantifier

A quantifier can be added to the end of a specific letter, character set, or sub-expression.  Here are three examples.

```bash 
[a-z]{3,}  # Match 3 or more lowercase letters
(some phrase){1} # Match "some phrase" exactly once
a{6} # Match the letter a exactly 6 times (i.e. aaaaaa)
```

#### Literal Matching

A literal match is exactly what it says.  It is an exact match for a given text.  If I had the following group of text: 

```
Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book.
```

I could match the phrase "unknown printer" with a literal regex.

```bash 
echo "... text to match ..." | grep -P "unknown printer"
```

A literal match is another way of doing a standard search on text.  A sub-expression is very similar to a literal matching, but with literal matching, you do not have the ability to add a quantifier, an "or" condition, or select a sub-expression as a variable.  We can add a quantifier at the end of a sub-expression, so if we wanted to match the "gmail.com" or "yahoo.com" phrase exactly 1 time, we could add the quantifier to the end `(gmail.com|yahoo.com){1}`.

#### Example Regex

Let's say we had the following file called `email-addresses.txt`: 

```bash 
jon23@gmail.com
bob879@yahoo.com
not an email
sally2@customsite.com
fred.jones@hotmail.com
not an email address
```

Learning how to match all four of the emails with a single regular expression will demonstrate a lot of the concepts that we have covered.  We start by matching all characters ([here is a regex cheatsheet](https://www.rexegg.com/regex-quickstart.html) to refer to during this process).

```bash 
cat email-addresses.txt | grep -P "^.+"
```

The expression we just wrote means that we are starting at the beginning of each line and looking for _any_ character except line breaks in a quantity of 1 or more characters.  The `^` means we are starting at the beginning of the line, the `.` means we are matching all characters except line breaks, and the `+` is a quantifier that means 1 or more matches.  We could easily have written the same expression differently like so: 

```bash
cat email-addresses.txt | grep -P "^[^\r\n]{1,}"
```

As you can see, regular expressions can be used in a multitude of ways.  In this version, we are doing the _same_ thing we did above with different syntax.  The `^` still indicates that we are looking at the beginning of each line.  The `[^\r\n]` means that we are matching _any_ character that is _not_ (`^`) a carriage return or new line (`\r`, `\n`).  Notice how when we place the `^` inside the character set it now acts as a negation rather than "search from the beginning of the line".  Symbols behave differently when placed inside a character set, so be careful!  Finally, we want to match these characters 1 or more times, so we use the `{1,}` syntax.  The comma after the 1 indicates that we want 1 or _more_ matches.  Anyways, if we run this, we will again match all six lines of the text file.  Since we only want to match the email addresses, we will need to tweak the expression.  Moving forward, I will be writing two regular expressions with different syntax that both do the same things.

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

### Bash Scripting

Throughout this tutorial, we have covered many commands and concepts.  Most of the commands we have learned (with the exception of the `awk`) command are for usage on the command line, but what if you wanted to put some of these in a script to run?  You could always write out a long and elaborate command and execute it, but it will not get saved and is difficult to write.  Bash scripting solves this problem by allowing you to write common bash commands within a script file and then execute that script file.  You may find this useful if you need to do something on a periodic basis.  For example, maybe you need to clean out a specific folder on your computer every day and place the contents of it in an archived folder with today's date on it.  You can do this through bash scripting, and in this brief introduction, I will teach you how to do just that.  First, we need to understand the basics of a scripting file.

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

Notice that the top of the file has something called a "shebang" (`#!/bin/bash`), which tells the interpreter which executable to run the script against.  In this case, we are telling it to run with the bash shell which is stored in `/bin` on our machine.

This is the most basic form of a script, but obviously not all that useful.  Throughout this section, we will learn the most important components of a bash script, which includes: 

* Variable declarations
* Built-in variables
* Command line arguments
* Reading user input
* for loops
* if-then statements

With these concepts, you should be able to accomplish 95% of your tasks.  Sure, there will be times where the above concepts are not enough, but again, this is an introduction to scripting rather than a deep dive.  For the rest of this section, I will be using a file called `shell-scripting-basics.sh` unless otherwise noted.  

#### Variable declarations

```bash 
MY_VARIABLE="some value"

echo $MY_VARIABLE
echo "Variables can also be added within double-quoted strings: $MY_VARIABLE"
echo 'But not single-quoted strings. This will not read the variable: $MY_VARIABLE'
```

Declaring and using variables is rather simple in bash scripting, so I will not spend a lot of time here.

#### Built-In Variables

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

#### Command Line Arguments

A shell script can take arguments on the command line.  For example, if I ran the following script: 

```bash 
./shell-scripting-basics.sh 3 9
```

and then here is the code in the script: 

```bash 
#!/bin/bash

echo "The script $0 evaluates to: " $(($1+$2))
```

This script will evaluate to - "The script ./shell-scripting-basics.sh evaluates to:  12"

As you can see, we can use the built-in variables inside our scripts.

#### Reading user input

We can also read user input from a script.  This is similar to reading arguments, but instead of the user typing their input in before execution time, they type it in during execution.

```bash
#!/bin/bash

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

#### for loops 

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
for item in *; do
        echo $item
done
```

#### if-then statements

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

There are endless possibilities to writing bash scripts.  Play around a bit and you will be on your way!

### Virtual Machines and SSH Protocol

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
4. If the keys validate, the user now gets remote access to the VPS.

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

This should successfully log you into your VPS.  The last thing I want to show you is how to transfer files to and from your remote machine and local computer.  To do this, we use the `scp` utility.

#### From local computer to remote machine

If I had `sample-file.txt`, the way I would upload this to my remote machine is like so: 

```bash 
scp -r sample-file.txt root@157.230.167.2:~/
```

This will upload the `sample-file.txt` file using the `root` user and place that file in the home directory `~/` on my remote machine.  You can specify any path to place it on your remote machine so long as you put a colon `:` after the IP address.

#### From remote machine to local computer

To download that same file from your remote machine to your local computer, you would just run the following.

```bash 
scp -r root@157.230.167.2:~/sample-file.txt ~/Downloads
```

This will place that same sample file into my local computer's `~/Downloads` folder.

#### Downloading packages to your remote machine with wget

Sometimes, you will need to download software packages from the internet to your VPS.  Since you do not have a GUI to work with, you must do this with the command line.  Let's say I wanted to download a Google image to my VPS for whatever reason.

Here is a nice picture of a Husky - https://cdn.orvis.com/images/DBS_SibHusky.jpg

We could download this to our VPS using the following command.

```bash 
wget -O my-custom-picture.jpg https://cdn.orvis.com/images/DBS_SibHusky.jpg
```

This will download the photo and save it as `my-custom-picture.jpg` in whatever directory I execute the command from.

#### Using VSCode with your remote machine

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

### Networking on Command Line
### Process Management

Process management sounds like an intimidating concept, but for the average bash user, there are only a few things that you will ever have to deal with regarding processes.  I could list out a bunch of commands here for you, but they will not make any sense unless you understand the sequence that the bash shell (and kernel) takes when a new process is started.  When your computer starts up, the kernel will call a process called "init", which on a UNIX-based operating system is usually the script called `init` located at `/sbin/init`.  Once this process has started, all other processes will be started by other processes.  This does not make a lot of sense starting out, but once you know how a process starts, you will gain clarity into what goes on behind the scenes on your computer.  A process can start another process (usually the terminal starting a new process as a result of a command typed into it) by first creating a copy of itself, and then executing the new command within that copied process.  Here is a visual to better explain: 

{% asset_img processes-linux.png %}

We can also simulate this process in a terminal ourselves using the `strace()` command.

```bash 
strace ls
```

When you run this command, you will see all of the system calls that were made when you ran the `ls` command.  Although `ls` is considered a "command" in many people's minds, it is really just another process.  I have cut out parts of the `strace ls` output below and highlighted some of the most important parts.

```bash 
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

#### ps and top commands

There are two commands that give us output relating to the processes that are currently running on our computer.  We will start with `ps` because it has more of the 


### System Management

