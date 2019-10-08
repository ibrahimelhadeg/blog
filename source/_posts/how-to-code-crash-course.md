---
title: How to Code [The Ultimate Crash Course] in 2019
date: 2019-01-05 14:26:26
tags: how to code, coding, teach yourself code
---

**Note Before Starting**: If you run into problems with anything, [please refer to this post](/blog/2019/how-to-raise-issue-github/) which will show you the proper way to raise an issue on Github for me to best answer your question.
 
**Repository**: [Here is the repository this post is associated with.](https://github.com/zachgoll/how-to-code-1-hour-crash-course)

This short crash course should be paired with the YouTube video below. Although the YouTube video covers all the topics in this post, you will find extra explanations throughout this post that are not in the video.

<iframe width="560" height="315" src="https://www.youtube.com/embed/wk80oPXKmk0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Who is this course for?

This course has zero prerequisites. I have designed it for the complete beginner and although you will not be fluent in C, Java, Javascript or Python (etc.) by the end of it, you will have a roadmap to guide you for months, or even years to come. The goal of this tutorial is not to make you proficient in any one area but to expose you to many of the things you will encounter as a programmer. With that in mind, this tutorial is also not going to be a massive resource list. I know there are plenty of resources to learn how to code out there, and honestly, there are plenty of _awesome_ resources. Learning to code is not a matter of finding the good stuff but rather being selective and disciplined to know when enough is enough. This tutorial will provide tools and resources, but not so many that you feel overwhelmed.

As stated, this tutorial will not suddenly turn you into a proficient programmer, but I do believe if you follow along intently and commit yourself to completing the courses and resources mentioned at the end of the post, you _will_ become a proficient programmer. This stuff can be challenging at first, so have patience, keep your expectations in check, and most importantly, enjoy the process of learning to code! This is a skill that will literally change the way you live, work, and even think. Along the way, you will have tons of challenges, but the daily successes that coding offers will keep you going!

Github Repository
-----------------

Although I would assume many viewers of this tutorial are not familiar with Github, I have included something called a repository<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">A repository is just a fancy word for a folder with a bunch of folders and files in it. Github is the site which hosts thousands of repositories from thousands of developers across the world.<i class="fa fa-window-close close-it"></i></span></span> (throughout the post, click the info buttons for further explanations) to provide accompanying resources and to introduce you to "git"<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">There is a huge difference between Github the website and git the software. Github just happens to use git _and_ be the most popular website for hosting code and collaborating on projects. The actual git software is used on other code hosting sites like Gitlab, Bitbucket, Sourceforge, or even a custom host! When I refer to “git”, I am talking about the [specification](https://git-scm.com/) that allows you to manage versions of projects. which is something you will use for the rest of your coding life.<i class="fa fa-window-close close-it"></i></span></span>

Topics
------

Throughout this 1 hour crash course, we will be covering the following topics.

*   Can anyone code? What is coding anyways?
*   What language should I learn first?
*   Brief introduction to how a computer works
*   Basics of Coding through Examples
    *   Terminal Basics
    *   Variables
    *   Common Data Types
    *   Loops
    *   If-Then Statements
    *   Operators
    *   Functions
    *   Git
*   What is a “Coding Environment” and how do I set mine up?
    *   Local (Your Computer)
    *   Hosted (Virtual Private Server)
    *   Virtual Machine / Containerized (Docker, Vagrant)
*   Types of Coding
*   Additional resources and reasonable expectations of improvement

Can Anyone Code?
----------------

Ahhh… One of the most popular questions among aspiring programmers. I have a dead simple answer for you. YES. Some people are going to be better than others, but please don’t let doubts about your intelligence prevent you from teaching yourself to code. Believe it or not, your brain is “coding” every day of your life. A great example of this is the task of cleaning your room. Everything on your floor needs to be put away, and different items will be put away in different locations. For simplicity, we will assume that everything on your floor will either go in your closet or in the trash. Here is our program for cleaning our room written in “pseudocode”:<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">Pseudocode is a type of code that is not written in a specific language and would not actually work if you tried to run it, but it does lay out the basic logic that you want your code to follow. Many programmers will first write the pseudocode for their program and then turn that into real code. We will be turning this pseudocode into a real program later in this tutorial.<i class="fa fa-window-close close-it"></i></span></span>

```
For Each Item on the floor:  
 If Item is a piece of clothing:  
    fold it  
 else  
    throw it away  
```

Although what you just read may look slightly odd, we have demonstrated that the human brain “codes” every day, all day. To clean our room, we are “looping” through each item on the floor and performing an “if-then” logical statement to decide based on a condition (whether it is a piece of clothing or not) what we want to do with the current item from the floor. This example may sound trivial and easy, but once we dive into the actual act of writing code, you will find it hard to see the big picture like this. So returning to the original question of “Can anyone code?”–If you can clean your room, then you can code. You just need to be willing to devote a large amount of time up front to learn how to instruct your computer to do what you want it to do. In short, quit wondering if you are capable of this thing we call “coding” and just START.

What Language Should I Learn First?
-----------------------------------

At one point, I was a culprit of asking this question, but I’m here to tell you to STOP ASKING THIS QUESTION. You will soon find out that most programming languages work in a similar way to one another and therefore your understanding of one will translate fairly smoothly to another. Just follow along with the tutorial and absorb the concepts as best you can. You can come back to this question later.

If you are like me, you at least want to know some of the popular languages, so here is a very short list. 99% of you will be writing code in one of these languages at some point.

*   Python
*   Javascript
*   Java (no relation to javascript)
*   PHP
*   C / C++
*   Ruby

What is Coding Anyways??
------------------------

Later, we will go into some of the different professions you can do as a coder, but from a 10,000 foot view, what is coding? You will get many different answers to this question because coding is not one thing. At its core, code is simply some text sitting in some file that a human has written<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">As code gets more complex, we will see more and more computers writing the code. Originally, humans had to write every last instruction for a computer to do anything useful. Nowadays, many artifical intelligence can actually write its own code.<i class="fa fa-window-close close-it"></i></span></span> and a computer can run. It sounds boring, but from this simple concept we have created cars that drive themselves, a world wide web that connects humans across the globe, and [robots that can dance better than myself](https://www.youtube.com/watch?v=hMtABzjslXA).

In my opinion, code is just a generic word we use in an attempt to conceptualize a topic that is far too complex to explain in a single sentence. I have found that explaining what code is can be futile without more context on the subject, so let’s dive in!

Brief Introduction to How a Computer Works
------------------------------------------

When I first started learning to code, it troubled me that I did not understand how the computer I was coding on worked. Although you would need years of study to fully understand the inner workings of a computer, I wanted to explain the very basics of it before we start learning how to code. Once you have mastered the basics of coding, there is a book/course called [Nand 2 Tetris](https://www.nand2tetris.org/) that is a great resource for learning this topic.<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">As a forewarning, this book/course will take you several weeks or even months to finish, and requires at least a small amount of background in coding to complete. I do not suggest starting your coding journey with this course.<i class="fa fa-window-close close-it"></i></span></span>

So how does your computer display a YouTube video in HD? How does your computer download Gigabytes of data in just a few minutes? How does your computer calculate complex numbers in milliseconds?

**Abstractions**

To understand a computer, you need to be able to understand what an “abstraction” is. In day to day life, we see abstractions all the time. For example, when you turn on the shower in the morning, that is an abstraction. Naturally, the water will not come out of the faucet. To make this happen, water has to be collected, directed to your house, heated, and finally sent through pipes until you find yourself taking a nice hot shower. Every time you get into the shower, you blindly expect that turning the knob will instantly start the water. The end user of the shower (you) only needs to know how to turn the knob. The rest of the process is taken care of by either automated equipment or a water company.

Coding works the same way. You start with something readable, and end with 1s and 0s. Go ahead and follow the steps below.

1.  Sign up for a free account on [Cloud9](https://c9.io/) - We are doing this to avoid having to download and configure our local computers to run the code I am about to show.<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">We will talk about coding environments later in the tutorial, but for now, you are going to have to just go with it. As you learn more about coding, you will realize how much of a pain it can be to get everyone’s computer working the same.<i class="fa fa-window-close close-it"></i></span></span>
2.  Create a new workspace, give it a name (does not matter), and select the “C++” template.
3.  Once loaded, you should see a file pane on the left, and a terminal at the bottom of the screen. Again, do not worry about understanding all of this yet. Double click the file called `hello-c-world.c` to open it up. You should see the following.

```java
#include <stdio.h>  
  
main() {  
 printf("Hello World!\n");  
}  
```

This is a super simple C (the C programming language) program that will print the phrase “Hello World” to your terminal when you execute it. So far, although the code may look a bit unfamiliar, anyone could interpret the general idea of what this program does. You could reasonably guess that `main()` is the name of the function and `printf()` will print something somewhere. There is just one problem… How is our computer going to read this??

4.  In the terminal at the bottom of the screen, type the following command and press Enter.

```java
gcc -S hello-c-world.c  
```

Did you catch that? In the left-hand file pane, a new file was created called `hello-c-world.s`. Go ahead and open that file up. You will see several more lines of code, but this time, the code is not so intuitive.

```
 .file	"hello-c-world.c"  
 .section	.rodata  
.LC0:  
 .string	"Hello World!"  
 .text  
 .globl	main  
 .type	main, @function  
main:  
.LFB0:  
 .cfi_startproc  
 pushq	%rbp  
 .cfi_def_cfa_offset 16  
 .cfi_offset 6, -16  
 movq	%rsp, %rbp  
 .cfi_def_cfa_register 6  
 movl	$.LC0, %edi  
 call	puts  
 popq	%rbp  
 .cfi_def_cfa 7, 8  
 ret  
 .cfi_endproc  
.LFE0:  
 .size	main, .-main  
 .ident	"GCC: (Ubuntu 4.8.4-2ubuntu1~14.04.3) 4.8.4"  
 .section	.note.GNU-stack,"",@progbits  
```

This is what we call “assembly code”. We are now one step closer to being able to execute our code file with our physical computer. In the above command, we used the [GCC compiler](https://en.wikipedia.org/wiki/GNU_Compiler_Collection)<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">When I say “GCC Compiler”, it sounds like I am referring to some physical object. In reality, the GCC compiler is simply hundreds of lines of code ([you can see them here](https://github.com/gcc-mirror/gcc)). In other words, we are using pre-written code to compile _our_ code. It can get confusing to say this, but we are using code to convert code into machine code.<i class="fa fa-window-close close-it"></i></span></span> to convert our human readable C code into some _more abstract_ version of code.

5.  In the terminal at the bottom of the screen, type the following command and press Enter.

```bash
gcc -o my-first-program hello-c-world.c  
```

In the previous step, we asked the compiler (GCC) to convert our C code into assembly code and then stop there. Instead of doing that, we now will tell the GCC compiler to convert our C code into assembly code and then our assembly code into binary code. As you can see, there is now a file called `my-first-program`. Double click on this file to open it. You will see a bunch of symbols and characters that make absolutely no sense at all.

```
>@@@x@8	@@@@@@��88@8@@@�� ``08 ((`(`��TT@T@DDP�td��@�@44Q�tdR�td``��/lib64/ld-linux-x86-64.so.2GNUGNUD�;���y��7Ԡ1I�J>" libc.so.6puts__libc_start_main__gmon_start__GLIBC_2.2.5ui	1�`` `(`H��H�  
 H��t�;H����5 �% @�% h������%� h������%� h�����1�I��^H��H���PTI���@H��@@H��-@�����fD�G`UH-@`H��H��w]øH��t�]�@`����@`UH-@`H��H��H��H��?H�H��u]úH��t�]H�ƿ@`����=Y uUH���~���]�F ��@H�=	 t�H��tU� `H����]�{����s���UH���@�����]�AWA��AVI��AUI��ATL�%� UH�-� SL)�1�H��H���m���H��t�L��L��D��A��H��H9�u�H��[]AA]A^A_�ff.���H��H���Hello World!;0,���|l���LY����l��������zRx����*zRx�$����@FJw�?;*3$"D����A�C  
KDd����eB�E�E �E(�H0�H8�M@l8A0A(B BBB�����  
```

These are actually 1s and 0s, but the text editor cannot understand them and prints nonsense. At this point, we have finally converted our original program into 1s and 0s which the computer can read. How does the computer know how to read 1s and 0s? Well, a computer is composed of transistors, which are neat little devices (super small) that fire binary signals (i.e. 1 or 0, true or false, white or black, etc.). Since we know there are only two states a transistor can be in (1 or 0), we can design a computer chip (the CPU, or “Central Processing Unit”) that can read a sequence of 1s and 0s and turn them into letters, numbers, and pixels on the screen. Again, I do not have time to go into this more deeply, but if you’re so curious you cannot contain yourself, read [Charles Petzold’s “Code” book](https://amzn.to/2ScR6iJ).

6.  In the terminal at the bottom of the screen, type the following command and press Enter.

```bash
./my-first-program  
```

When you pressed Enter, you should have seen “Hello World” print to the terminal.<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">In the programming world, when a program prints to the terminal this is also referred to as printing to “stdout”. This will make more sense later on.<i class="fa fa-window-close close-it"></i></span></span> The computer has read the 1s and 0s contained in your binary file and converted them to letters!

If we take a step back, we can see how the hundreds of different programming languages are possible. Heck, we could make up a language! Here’s an idea:

```python 
print "Hello World"  
```

How simple is that? As long as we can convert that line of text into the same 1s and 0s that our C program was converted to, we have just invented a language! Unfortunately, this language has already been invented, and it is called Python. [The Python interpreter](https://github.com/python/cpython) (similar to GCC compiler) will read this line of text and convert it to the same 1s and 0s that the GCC compile converted our C program to, and thus will do the same exact thing when executed in the terminal.

### Recap: How a Computer Works

Although this was a very brief explanation of how a computer works, the main thing I want you to take away is the fact that _all_ programming languages eventually end up looking exactly the same. Sure, there are different CPUs (Linux vs. Mac vs. Windows) which have slightly different requirements for those 1s and 0s, but as a generalization, it is safe to say that all programming languages end up looking identical at the lowest level of _abstraction_.

### Resources Mentioned in this Section

*   [Code by Charles Petzold](https://amzn.to/2ScR6iJ)
*   [Nand 2 Tetris](https://www.nand2tetris.org/)

### Let’s Setup your Coding Environment!

To reiterate, for this tutorial, we will be using a “hosted” environment called Cloud9. This hosted environment will have Python and Git pre-installed so we do not have to deal with the installation process. Follow the steps below.

1.  Go to Cloud9 and create a new workspace
2.  Name the workspace whatever you want
3.  If you have not already, go to [Github](https://github.com) and create an account
4.  Once signed in to Github, [go to this link](https://github.com/zachgoll/how-to-code-1-hour-crash-course)
5.  In the upper right corner of the screen, click the button that says “Fork”. This will clone a copy of my sample repository to your account so you can edit it.
6.  Once the fork has finished, navigate to your newly created repository and find the button that says “Clone or download”. Click this button to copy the link to the repository.
7.  Go back to the Cloud9 setup and paste this link into the field that says “Clone from Git or Mercurial URL”
8.  Select “Python” as the template
9.  Click “Create Workspace”
10.  When the workspace has loaded, you will see all the files on the left and a terminal at the bottom of the screen. Type the word “python” in the command line and press Enter.
11.  You are now in a “shell” where you can execute Python code. Type “2+2” and press Enter. Get the gist?
12.  Press ctrl-D to exit the Python interpreter

Let’s recap what we have just done. First, we created a Cloud9 workspace (i.e. we asked the company called Cloud9 to start an Amazon Web Services Virtual Private Server). We then “forked” my Github repository to your account and configured the Cloud9 VPS to download your newly forked repository to the home folder. Finally, we configured the VPS to download Python to it and ran a simple command. Do not worry if you are slightly lost in these steps.

Basics of Coding
----------------

No matter what language you are writing code in, there are just a few concepts that you _must_ understand. If you can understand these few essentials, you will be able to learn any coding language. To better conceptualize the basics, we will return to our room cleaning exercise and actually put it into code! Let’s start with the actual code and then walk through each piece to introduce the concepts.

```python
# This is a Python comment.  If I place the hashtag at the beginning of the line,   
# the Python interpreter will ignore it!  Other languages may use different symbols   
# for comments.  For example, if you want to make a comment in HTML, you would  
# enclose your comment text between two carets   
# <!-- this is where your comment goes -->   
# In javascript and PHP, you can use a double slash for comments   
# // this is a javascript or PHP comment  
  
# This is a function.  It takes an array as an argument.  
def sort_clothes(items_array):  
 # This is the main part of our program.  We are 'looping' through each item   
 # in our items_in_room array and logically deciding where to put them  
 for item in items_array:  
    
 # If the value of the item's 'type' property is clothing, we want to put that item in the dresser  
 if item["type"] is "clothing":  
 print("I put the " + item["description"] + " in my dresser!")  
    
 # If the value of the item's 'type' property is not clothing, this means   
 # that it must be trash so we can throw it in the trash  
 else:  
 print("I threw the " + item["description"] + " in the trash can!")  
  
# This is a simple string variable that stores the name of our program  
name_of_this_program = "clean-room.py"  
  
# Item 1, 2, and 3 are all called 'objects'.   
# Each object has a type property and a description property  
item1 = {  
 "type": "clothing",  
 "description": "hoodie"  
}  
  
item2 = {  
 "type": "clothing",  
 "description": "t-shirt"  
}  
  
item3 = {  
 "type": "trash",  
 "description": "orange peel"  
}  
  
# This is an array.  An array stores a list of variables, objects, other arrays, etc.  
# We need to put the items in this array so we can loop through them and sort the items in the room!  
items_in_room = [item1, item2, item3]  
  
  
# A simple Python print statement that will print out the name of the program  
# notice the + sign.  This combines two strings together!  
print ("Program: " + name_of_this_program)  
print("----------------------------------------")  
  
# We now call our function passing the array of objects as our argument  
sort_clothes(items_in_room) 
```

I know I know. This is a lot of code to look at! Go ahead and try to read through and make sense of it and then we will walk through the lines.

<iframe width="560" height="315" src="https://www.youtube.com/embed/8ghyK5YrQzQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### What is the Command Line?

You may have noticed that during the course of this tutorial we have already typed some commands into the command line. I’m here to tell you to get used to it because the command line is used for all types of coding whether it be machine learning or front-end web development. The command line is a powerful utility and allows you to do common tasks such as:

*   Create/Delete folders
*   Create/Delete/Modify files
*   Set permissions on files and folders
*   Print the contents of files
*   Interact with Github through the Git utility
*   Download software packages
*   Run your applications
*   etc.

If I asked you to run the program above, how would you do it? First, you need to get your “working directory” to match the “path” of the program. Then you need to run the program using the Python interpreter. Sound like jibberish?

**Step 1: Change your Working Directory**

_At this point, I would recommend watching the video. Although everything is documented in this post, following along with the video is a bit easier._

When you first login to your Cloud9 workspace, in the terminal area, you will see something like:

```bash
zachgoll:~/workspace (master) $  
```

Print the current working directory by typing `pwd` into the terminal and pressing enter. It will print `/home/ubuntu/workspace`. Hold on a second… If the current working directory is `/home/ubuntu/workspace`, then why does it say `~/workspace`? Well, every computer has what we call a “home” directory, and you can get to it by typing the following command.

```bash
cd ~  
  
# This is a bash comment  
# We could also just type cd without any arguments to get the same result  
cd  
```

In other words, `~/` is equivalent to `/home/ubuntu/` on this particular machine. Furthermore, we would call `/home/ubuntu/` a “path”. Our goal is to get the “path” of our current working directory that can be found by typing `pwd` (“print working directory”) to match the path of our Python program. Look at the folders and files in the left-hand pane. Click the folder that says `source-code`. You will see the file called `clean-room.py`. Now type the following two commands into the terminal. 

```bash
cd ~/  
ls  
```

The `cd` command will “change directory” to the supplied path. We typed `cd ~/` but we very well could have typed `cd /home/ubuntu/` or `cd` to get the same result. The `ls` command will “list” all of the files and folders located in your current working directory. In this case, you should see `/lib /workspace` printed. All our files are in `/workspace`, so let’s navigate there by typing `cd workspace` or `cd /home/ubuntu/workspace` or `cd ~/workspace`. Type `ls` again and you should now see `README.md put-your-code-here/ source-code/`. But what about those folders called `.c9` and `.git`? These are considered “hidden” folders because they are preceded with a dot. We can display them by typing the command `ls -la`. Finally, let’s navigate to the `source-code` folder. When you type `pwd` you should see `/home/ubuntu/workspace`. Now type `cd ~/workspace/source-code/` or `cd source-code/` or `cd /home/ubuntu/workspace/source-code/`. These will all translate to the same “path” of `/home/ubuntu/workspace/source-code/`. Type `ls` and you will see the `clean-room.py` file.

You can run this file by typing `python clean-room.py`. So why did we have to navigate to the same directory the program is in to run it? We didn’t actually need to do this. We did this for simplicity, but we could have also run the program with any of the following commands.  

```python
python clean-room.py  
python ./clean-room.py  
python /home/ubuntu/workspace/source-code/clean-room.py  
python ~/workspace/source-code/clean-room.py  
  
# Navigate to a different working directory and run it  
cd ~   
  
# By placing a period before the slash, we indicate a "relative" path  
python ./source-code/clean-room.py  
```

At this point, you are probably feeling a bit confused, and that is okay. To recap, we haved talked about the “current working directory”, a “path”, the `cd` command, and the `ls` command. Mastering the command line is all about practice, but you truly need to understand these four things well. You also need to understand the difference between an absolute path and a relative path. Type the following command. 

```bash
cd /home/ubuntu/  
```

From this path, what is the relative path to the `clean-room.py` program? The relative path would be `./workspace/source-code/` because we are two directories above the directory the program sits in. From this directory, we could run the program with `python ./workspace/source-code/clean-room.py` or we could navigate to the program directory with `cd ./workspace/source-code/` and then run it with `python ./clean-room.py`.

Type the following command.

```bash
cd ~/workspace/source-code/  
```

From this working directory, how would you get your current working directory to equal `/home/`? There are two methods. The first way you could do this is by typing the absolute path.  

```bash
cd /home/  
```

The second method would be as follows.
  
```bash
cd ../../../  
```

The double dots mean that you are moving up one directory. Since `/home/` sits 3 directories above `source-code`, we need to type it three times.

Great! You now have at least a basic grasp on navigation between directories, relative paths, absolute paths, and running programs. We now must cover the other essential command line skills. If I asked you to copy the program we just ran to your own file and run it, how would you approach this? Would you open up my program, right click, copy, and then create a new file and paste the code in? This would certainly work, but we can do it quicker via the command line. Let’s first create a folder to put our program in. 

```bash
# Navigate to the ~/workspace/place-your-code-here/ path  
cd ~/workspace/place-your-code-here/  
  
# Create a new directory  
mkdir clean-room-program-folder  
  
# Navigate into this new directory  
cd clean-room-program-folder/  
  
# Create a new file  
touch clean-room.py  
  
# Copy the contents of the source file to your new file  
cp ~/workspace/source-code/clean-room.py clean-room.py  
  
# Run your program  
python ./clean-room.py  
```

In this snippet, we learned three new commands, all of which you will use frequently. The `mkdir` command will create a new directory with a name of your choice. The `touch` command will create a file with a name of your choice, and the `cp <old file path> <new file path>`<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">You may have noticed that I have used the “<” and “>” symbols throughout the post. Although these symbols are used in HTML and as operators to some languages, they are also frequently used in coding tutorials to indicate a piece of text that is specific to the end user. In this case, you should not actually type <old file path> and <new file path>. You should replace them with the actual paths.<i class="fa fa-window-close close-it"></i></span></span> will copy the contents of the file at the old file path to the new file. Last, let’s say you no longer wanted to have this file sitting in the folder you just created. Run the following commands to move the file up one directory and delete the old directory.

```bash
cd ~/workspace/place-your-code-here/clean-room-program-folder/  
mv clean-room.py ../  
cd ../  
rm -rf clean-room-program-folder/  
```

All I have to say is BE CAREFUL with the `rm -rf` command. This is the command you must use to delete directories and it can be dangerous if you are not careful about which directory you delete. If you ran something like `rm -rf /`, you would be deleting everything on your computer!!! There is no undo button for this kind of mistake.

Finally, let’s say you are done with this program and want to delete it altogether. Navigate to the directory it is in, and then run the `rm` command.

```bash
cd ~/workspace/place-your-code-here/  
rm clean-room.py  
```

**Recap** - There are far more commands you can run on the command line, and during this tutorial, we only covered those that can be run on Mac and Linux. Windows will be a little different, so if you know that you will be developing on Windows, be sure to learn the equivalents of the commands shown above for your machine, or [just use the Bash shell for Windows 10 and above](https://www.howtogeek.com/249966/how-to-install-and-use-the-linux-bash-shell-on-windows-10/).

The commands we covered are all essential to coding and are listed below:

*   `cd`
*   `pwd`
*   `ls`
*   `mkdir`
*   `touch`
*   `cp`
*   `mv`
*   `rm`

If you need help using these commands, you can always run `man <command>` in your terminal, or type “How to use <command>” into Google. As an example, typing `man cp` into your terminal will print a manual page for this command.

```
CP(1)                     BSD General Commands Manual                    CP(1)  
  
NAME  
 cp -- copy files  
  
SYNOPSIS  
 cp [-R [-H | -L | -P]] [-fi | -n] [-apvX] source_file target_file  
 cp [-R [-H | -L | -P]] [-fi | -n] [-apvX] source_file ...  
 target_directory  
  
DESCRIPTION  
 In the first synopsis form, the cp utility copies the contents of the  
 source_file to the target_file.  In the second synopsis form, the con-  
 tents of each named source_file is copied to the destination  
 target_directory.  The names of the files themselves are not changed.  If  
 cp detects an attempt to copy a file to itself, the copy will fail.  
  
 The following options are available:  
  
 -a    Same as -pPR options. Preserves structure and attributes of files  
 but not directory structure.  
  
 -f    If the destination file cannot be opened, remove it and create a  
: 
```

### Variables

Now that you have a basic understanding of how to use the terminal and run your program, let’s dig into the code itself. Again, do not become discouraged by the code. I have purposefully made it a bit more challenging than most “learn to code” tutorials because I believe the unity of an entire program will help tie the concepts we cover together in the long run. The first concept we need to cover is that of variables. You can think of a variable as a _container_ for different types of data. In the `clean-room.py` program above, find lines #8, #13, #18, and #25. These are all variables.

Before we get too far into this, I want to mention that the language we are coding in (Python) is a _loosely typed_ language, which means that you do not have to specify the type of variable you are declaring. In languages like C or Java, you would need to signify the type of variable you are declaring. In order to truly understand these variables, we need to do a quick lesson on data types.

#### Data Types

In programming, there are several data types, and depending on the language there could be more. I chose Python as an introductory language because it avoids some of the more complex data types and reflects the real world more than a language like C does. In 2018, most languages that you would find in demand are called _high level_ languages, which means they are even more abstracted than a language like C. The best way to explain this is through an example. The following Python code is the equivalent to the C code after it.

```python
item1 = {  
 "type": "clothing",  
 "description": "hoodie"  
}  
  
item2 = {  
 "type": "clothing",  
 "description": "t-shirt"  
}  
  
item3 = {  
 "type": "trash",  
 "description": "orange peel"  
}  
  
items_in_room = [item1, item2, item3]  
  
print(items_in_room)
```

^ THIS - EQUIVALENT TO - THIS

```java
#include <stdio.h>  
#include <string.h>  
  
struct Item {  
 char* type;  
 char* description;  
};  
  
int main()  
{  
 struct Item item1;  
 struct Item item2;  
 struct Item item3;  
 struct Item items_in_room[3];  
  
 item1.type = "clothing";  
 item2.type = "clothing";  
 item3.type = "trash";  
  
 item1.description = "hoodie";  
 item2.description = "t-shirt";  
 item3.description = "orange peel";  
  
 items_in_room[0] = item1;  
 items_in_room[1] = item2;  
 items_in_room[2] = item3;  
    
 int i;  
 for (i = 0; i < 3; i++) {  
 printf("Item %d Type: %s\tDescription: %s\n", i+1, items_in_room[i].type, items_in_room[i].description);   
 }  
 return 0;  
}
```

I don’t know about you, but I certainly prefer the Python version of assigning variables. C is a strongly typed language, which means for every variable we define, we must also tell the GCC compiler what _type_ of variable it is. In this example, we had to define a “struct” (i.e. “object”) that has two data members, both of type “string”. We then had to define an array of the struct type and individually assign values to that array. If none of this makes sense, just trust me when I say Python is easier to write and far more powerful than C.

In the C programming language, we need to know about data types such as char, unsigned char, short, int, bool, float, double, long double, void, and more. In the Python programming language, all of these data types still exist, but unlike C, we as programmers do not need to worry about them. We can dynamically assign and reassign any type of data to a variable.

```python
# This is a string data type  
var1 = "This is a string variable"  
  
# This is an int data type  
var2 = 1234  
  
# This is a double data type  
var3 = 1.234  
  
# This is a bool data type   
var4 = false   
  
# This is an array data type   
var5 = ["string value", 12344, 12.324, { "property1": "value1" }, false ]  
  
# This is a struct (object) data type   
var6 = {  
 "property1": "value1",  
 "property2": 2345,  
 "property3": {  
 "subproperty1": {  
 "sub-subproperty1": false  
 }  
 }  
} 
``` 

As you can see, we can create complex data structures without having to define their data types beforehand. So what are these data types we are talking about? You probably have started to catch on already, but there are 5 main data types that you need to understand.

1.  String
2.  Integer
3.  Boolean
4.  object
5.  Array

You will be using these data types for at least 95% of your coding needs. Here is an example of each in Python with explanations in the form of comments.

```python
# String  
#-------------------------------------------------------------------------------------------------------------------  
# Notice how there are numbers in this line.  Since they are surrounded by quotes, they are not considered numbers  
my_string = "I code 365 days of the year"  
  
# You can also use single quotes to define strings   
my_string_with_single_quotes = 'I code 365 days of the year'  
  
# Integer   
# An integer is simply a whole number  
my_integer = 397  
  
# A Double is very similar to an integer, but has decimals  
my_double = 3.97  
  
# Boolean  
# This one is easy.  It is either true or false.  No other possibilities.  
my_boolean = False   
  
# Object   
# Objects are not truly a 'data type', but we can think of them as such.  There   
# are endless possibilities with objects.  As you can see, we can nest values within our objects  
simple_object = {  
 "property_1": "has a string value",  
 "property_2": {  
 "type": "integer",  
 "value": 900  
 }  
}  
  
# Arrays   
# Arrays can be thought of as a 'list' of data   
my_string_array = ["first string", "second string", "third string"]  
```

Although you will have many questions regarding these data types, what I have layed out are the basics. We are now fully equipped to return to our discussion on variables. As you may have noticed, we are assigning all of this data with an equals sign. To the left of the equals sign is called the “variable” and to the right is the data. In our `clean-room.py` program, we have 5 main variables. First, we have `name_of_this_program` variable, which is assigned a _string_ value. Second, we have `item1`, `item2`, and `item3`, which are all assigned _object_ values (with _string_ property values). Finally, we have `items_in_room`, which is an _array_ of _objects_.

Defining a variable is simple in Python. The only requirement is that the variable be a single phrase. Below are examples of how to declare a variable and how not to.

```python
# These are valid variable declarations   
my_variable = 200  
myVariable = 200  
MYVARIABLE = 200  
MyVariable = 200  
  
# These are invalid variable declarations   
my variable = 200  
"my variable" = 200 
```

Different languages will have syntax<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">When I say “syntax”, I am referring to how your code looks. Many languages will have syntax standards that define how you should write different types of variables, functions, classes, etc. These standards are not mandatory for making your code work, but are considered “best practice” and will make things easier to read for other programmers.<i class="fa fa-window-close close-it"></i></span></span> standards. For example, in javascript, you would probably define your variables in camel-case. This means that each word is capitalized.

```javascript
// In javascript, we use camel-case  
myVariable = 200  
```

In a language like Python or PHP, we use underscores.  

```python
my_variable = 200  
```

Finally, you might notice that across ALL languages, a fully uppercase variable is considered a constant. In other words, defining a fully uppercase variable tells other programmers that this variable is set once and will never be modified throughout the program.  

```python
MYVARIABLE = 200  
```

### Loops

Loops are one of the most fundamental concepts in programming. In the real world, there is tons of data floating around, and it can all be structured in a way that we can loop through it. Take a practical example like a Twitter feed. Some users have thousands of tweets on their account, and it would be impractical to manually scroll through each of them to find a certain word or phrase. If I wanted to loop through the MIT Technology Review account’s tweets and count the number of external hyperlinks, it would take me days to do manually. With Python, I could retrieve all MIT’s tweets via an API request<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">An API is an “application programming interface”. You do not need to understand this yet. Just know that there are tons of APIs out there, and they are what connect applications on the web. For example, if I was building myself a website and I wanted to sell something, I might need to enable Paypal on my site. To do this, I have two options. First, I could use one of the Paypal buttons that is preconfigured to work in HTML. Second, I could get more advanced and connect to the Paypal API. Once connected to the Paypal API, I could write whatever code I want to enable payments on my site. While putting the button on my site and avoiding the API works, with the API, I could customize my options more.<i class="fa fa-window-close close-it"></i></span></span>, loop through all the tweets, and perform an if-then statement to determine whether the current tweet has a link in it. If it does have a link, I would then increment a counter. Below is some pseudocode.

```python
number_of_links = 0  
  
for each tweet in all_tweets:  
 if tweet has hyperlink:  
 number_of_links = number_of_links + 1  
  
print(number_of_links)  
```

Rather than me spending my entire day manually counting tweets, I can write this program in an hour and count them in a few seconds. This is the power of loops. In our `clean-room.py` program, we are looping through a small array of three items. In Python, the syntax is:
  
```python
for item in items_in_room:  
 # Print the item  
 print(item)  
```

In other languages, this loop may look a little different, but will have the same basic functionality. See below for this same loop in javascript.

```javascript
items_in_room.forEach((item) => {  
 console.log(item);  
});  
```

You could also do it like this in javascript.
 
```javascript
for (let i = 0; i < items_in_room.length; i++){  
 console.log(items_in_room[i]);  
};
```

Yes, the syntax is a little different in all three loops, but they all achieve the same thing. Even a year down the road, you will find yourself constantly looping through large sets of data and manipulating or extracting something from each individual piece of data.

### If-Then Statements

An if-then statement is something anyone can relate to. As in our `clean-room.py` program, we are asking ourselves whether each item is a piece of clothing or a piece of trash and then doing something different based on that piece of information. If it is a piece of clothing, we fold and put it away, and if it is a piece of trash, we throw it away. A very common coding pattern is to set up a loop and then perform an if-then statement on each item in the loop. If-then statements can also be used for more ad-hoc purposes. In the example below, we are setting up an if-then statement to control the amount of logging we do within our program.

```python
# At the top of the file, we create a single variable that we can set to true to enable "debug" mode   
# or to false to disable "debug" mode   
debug = True   
  
my_array = [104, 32, 63]  
  
for item in my_array:  
 if debug:  
    print("Original value: " + str(item) + " New value: " + str(item * 2))  
 else:  
    print(item * 2)  
```

In this example, if we are in debug mode, the program will print the following output.

```
Original value: 104 New value: 208  
Original value: 32 New value: 64  
Original value: 63 New value: 126  
```

If we are not in debug mode, the program will print a much simpler output.

```
208  
64  
126  
```

Although this is a simple program, if we had something more complex, it might be nice to have this kind of “switch” to go between a more verbose output and a simplified output.

### Operators

In the previous two sections, we saw how an if-then statement compares the current item value to an arbitrary value. To do this, the program is using an “operator”. An operator is something we use to compare or modify two pieces of information. Operators will look different in various languages, but all languages have a standard set of operators. Below are the most common operators you will use.

*   add
*   subtract
*   multiply
*   divide
*   modulus
*   increment
*   decrement
*   greater than
*   less than
*   equal to
*   and
*   or
*   not

Operators are the one area that I feel Python is a bit confusing in. Although Python is supposed to be intuitive, I feel that it just adds a bit of confusion to the subject since almost all other programming languages use the same set of operator symbols. For example, if I wanted to compare the value of two variables in Python, I would do it like this.

```python
var1 = 100   
var2 = 150  
  
if var1 is var2:  
 print("They are equal!")  
  
if var1 is not var2:  
 print("They are not equal") 
```

Compare this to the equivalent operation in javascript and you will see the difference.

```javascript
let var1 = 100  
let var2 = 150  
  
if (var1 === var2) {  
 console.log("They are equal!");  
}  
  
if (var1 !== var2) {  
 console.log("They are not equal");  
}
```

As you can see, the “equal to” operator and “not equal to” operator in Python is not the same as in javascript. In Python, the “equal to” operator is `is` while in javascript it is `===`. In Python, the “not equal to” operator is `is not` while in javascript it is `!==`.

Despite the differences, you need to remember that no matter what language you code in, you will always have the same operators available (although maybe in slightly different form). Let’s take a look at all the things we can do in Python with operators. If you go to your Cloud9 terminal and type `python`, you will enter a Python shell where you can type these commands and follow along.

```python
# Math operators   
2 + 2   
5 - 3   
4 * 8   
9 / 3  
8 % 4  
  
# Comparison operators - if they evaluate to true, they will print True, if not, they will print False  
2 is 2  
2 is not 5  
  
# Comparison operators - python also allows the traditional operators used in most languages.  
# These are the same operators as the ones used above  
2 == 2  
2 != 5  
  
# Comparison operators - all the rest   
2 > 1  
2 >= 2  
6 < 7  
6 <= 7  
2 <> 5 # another way to express "not equal"  
  
# Other operators   
2 == 2 and 3 == 3  
2 == 2 or 3 == 5  
  
# Increment and decrement  
my_var = 2  
print(my_var) # 2  
  
my_var += 1  
print(my_var) # 3  
  
my_var -= 1  
print(my_var) # 2  
```

Knowing all of the operators we have available to us, we could re-write our original if-then statement in the `clean-room.py` program multiple ways.

```python
# Original if-then statement   
if item["type"] is "clothing":  
 print("I put the " + item["description"] + " in my dresser!")  
else:  
 print("I threw the " + item["description"] + " in the trash can!")  
  
# New if-then statement  
if item["type"] == "clothing":  
 print("I put the " + item["description"] + " in my dresser!")  
else:  
 print("I threw the " + item["description"] + " in the trash can!")  
  
# Another way to write it  
if item["type"] <> "trash":  
 print("I put the " + item["description"] + " in my dresser!")  
else:  
 print("I threw the " + item["description"] + " in the trash can!")  
  
# Another way...  
if item["type"] != "trash":  
 print("I put the " + item["description"] + " in my dresser!")  
else:  
 print("I threw the " + item["description"] + " in the trash can!") 
```

You can see how there are multiple ways to write code! You will find later on that writing working code is the easy part. Writing _clean, well-written, working_ code is the hard part.

### Functions

The final concept you need to understand is functions. The simplest way to understand a function is in terms of inputs and outputs. You put something into the function, and you get something different out. In our `clean-room.py` program, our function looks like this.

```python
# This is a function.  It takes an array as an argument.  
def sort_clothes(items_array):  
 # This is the main part of our program.  We are 'looping' through each item in our items_in_room array and logically deciding where to put them  
 for item in items_array:  
    
 # If the value of the item's 'type' property is clothing, we want to put that item in the dresser  
 if item["type"] is "clothing":  
 print("I put the " + item["description"] + " in my dresser!")  
    
 # If the value of th item's 'type' property is not clothing, this means that it must be trash so we can throw it in the trash  
 else:  
 print("I threw the " + item["description"] + " in the trash can!")  
```

We “define” the function with the `def` keyword, and specify that the function will take an input called the `items_array`. The function will then loop through the `items_array`, and based on whether the item is a piece of clothing or piece of trash, will print a different sentence. A simpler function might look like so:

```python
def add_two_numbers(first_number, second_number):  
 return first_number + second_number  
```

This function will take two numbers as inputs and return a single output which is the sum of these numbers. But how do we use these functions? In order to use a function, we must “call” it. This is the same regardless of what language you are programming in. In Python, we can call the `add_two_numbers` function like so:

```python
add_two_numbers(1, 1)  
```

There is still a problem though. In our `sort_clothes` function, we can call it and it will print something to the terminal. With this function, when we call it, nothing seems to happen. This is because it is returning a value. We need to either store this value in a variable and then print the variable, or print the function directly. We can print the sum of the two numbers to the terminal like so:

```python
print(add_two_numbers(1, 1))  
  
# Or...  
the_sum = add_two_numbers(1, 1)  
print(the_sum)  
```

Both of these methods will print the number 2 to the terminal. As with our clothes sorting function, some functions do not return a value. If the function does not return a value, then you cannot assign it to a variable.

### Git

Using Git is not actually considered _coding_, but it is so commonly used that I consider it an essential of coding. Before we git into the details (yep, I’m real funny), we need to understand the reason _why_ Git is such a popular software. The reason we use Git is because it acts as an advanced “undo button”. By using Git, hundreds of coders can work on the same project at a time. Every change that is made to the project will be catalogued with the name of the engineer that made the change, and at any point, any engineer can _revert_ back to some previous state of the project. Let’s go back to the example of building a video transcription software in Python. Imagine that one day, you open your computer, pull down the latest changes to the project, and find out that someone has deleted all your files!!! Without git, you better hope that you had saved a local copy of the project, otherwise, you are out of luck. With git, the fix is very simple. You would simply run the following commands.

```bash
# This command will print all of the past changes to the project  
git log --oneline  
  
# Example output from the command above  
# 6fda0da I broke the project in this commit!  
# f2f170b Some routine bug fix  
# 6d54702 Add login functionality  
  
git checkout f2f170b  
```

What I have just done is found the state of the project _before_ my colleague deleted my files and reverted my version of the project to that version. I have essentially pressed a big undo button. I can now work from this version, make my changes, and fix the project.

I know this is theoretical and the commands above are rather advanced, but it demonstrates the power of git. For our learning purposes, I am only going to teach you the most basic and essential git concepts. You will need to understand the following.

1.  How to create or connect to a Git repository
2.  How to commit local changes
3.  How to push changes _to_ a repository
4.  How to pull changes _from_ a repository

**Concept 1: Setting up a new Repository**

First and foremost, you need to understand the difference between a _remote_ and _local_ repository. Every time someone says “remote repository”, this is referring to the Git repository somewhere outside of your computer. Think of some _remote_ village in the middle of nowhere (I think this is in Nepal).

![](/blog/2018/how-to-code-ultimate-crash-course/remote-village.jpg)

Imagine that your code project is sitting in one of those little houses in the remote village, and the only way you can update the code is by connecting via a hyperlink and a password. Now things get a bit confusing when I say “local” repository, because local could mean a lot of things. In most cases, the local repository is simply the group of folders and files that are stored on your physical laptop.

![](/blog/2018/how-to-code-ultimate-crash-course/local-laptop.jpg)

In our case, we are considering our “local repository” to be the Cloud9 server that we are coding on. For simplicity, imagine that the Cloud9 server we are using is sitting right next to you and is _local_. The entire idea behind git is to _push_ and _pull_ changes to and from the local and remote repositories. We are now going to go through an exercise that will expose you to the basic concepts of Git. At this point, you need to open a new browser. In the first tab, open the repository on your Github account. This is the repository that you “forked” (i.e. copied) from me. At the top left of the screen, it will say something like:

```bash
<username>/how-to-code-1-hour-crash-course  
forked from zachgoll/how-to-code-1-hour-crash-course  
```

Once you are on this page, open a _new_ tab with the Cloud9 workspace that we set up. To be clear, the _remote_ repository is the one on the Github tab, and you local repository is the one in your Cloud9 tab.

**Concept 2: Making Changes Locally and Committing them**

I am assuming at this point you have made at least one change to your local repository, but just in case, we are going to create a new file with our command line skills. Open the Cloud9 tab, go down to the terminal, and type the following command.

```
cd ~/workspace/  
touch learn-git.txt  
```

You have just created a new file at the “root” of your workspace called `learn-git.txt`. Double click that file to open it, and type anything into the file and save. At this point, type the following two commands.
 
```bash
git remote -v   
# Output of this command should look like below   
# origin  https://github.com/<your-username>/how-to-code-1-hour-crash-course.git (fetch)  
# origin  https://github.com/<your-username>/how-to-code-1-hour-crash-course.git (push)  
  
git status   
# Output should be similar (but not exact) to the following   
#Untracked files:  
#  (use "git add <file>..." to include in what will be committed)  
#        learn-git.txt  
```

By typing `git remote -v`, we are printing the URL to our _remote_ repository (i.e. the one sitting on Github’s servers). By typing `git status`, we are seeing what the git software is aware of on our local machine. You will probably see more than just the `learn-git.txt` file, but as you can see, git is telling us that the `learn-git.txt` file is “untracked”. We want this file to be tracked by git, so lets run the following command.

```bash
# Always make sure you are at the root of your workspace  
cd ~/workspace  
  
git add learn-git.txt  
  
# We could also run the following command to add ALL files  
git add .  
```

By running `git add learn-git.txt`, we are telling Git to add just the file we created. We also might have other files that are untracked, so we can run `git add .` to add everything at once. I use `git add .` frequently with my projects.<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">Although it may not be obvious, you will find that a “.” is used to refer to the entire current directory. With coding, you will see that the “.” is a multidimensional symbol. We can use it to access properties of an object (i.e. some_variable = object.prop), we can use it to refer to the current directory (i.e. cd ./), and we can use it to refer to all the files in the current working directory as we did here.<i class="fa fa-window-close close-it"></i></span></span>

Now, we can run the command `git status` and you will see something like the following.

```bash
git status   

#Changes to be committed:  
#  (use "git reset HEAD <file>..." to unstage)  
#  
#        modified:   source-code/clean-room.py  
#        new file:   test.txt  
```

We can now make our first “commit”, which you can think of as a formal declaration that you are making a change to the project. With each commit, it is advisable that you sign off on the commit with the `-s` flag, and you add a message to describe your change with the `-m` flag. See the structure of this command below.

```bash
git commit -s -m "Add my files to the remote repository on Github!"  
```

There is only one more step here. At this moment, all of your changes have been recorded and are recognized by the Git program, but if you go over to your Github repository right now, you will not see the files that you just added. To add them, we need to tell Git to push the changes we just “committed” from our _local_ repository to our _remote_ repository. When we are moving data from local->remote, this is called a “push”.

**Concept 3: Pushing Changes from Local to Remote**

```bash
git push origin master  
```

This command will _push_ all your changes to the “master branch” of the remote (it is referred to as “origin”) repository. Do not worry about understanding branches at this moment. If you navigate to your Github tab, you will now see the files!

**Concept 4: Pulling Changes from Remote to Local**

There is only one more thing that we need to understand about git. We need to understand how to pull changes from the remote repository down to your local repository. This is common when multiple people are working on the same project. Imagine that you do work from 8am - 5pm one day, and your colleague does some work from 5pm - 8pm. When you log in the next morning, your _local_ repository will be different than the remote repository and will not have all of the changes your colleague has made yet. Before you start working, you need to _pull_ your colleague’s changes down to your local repository.

1.  To do this, open up your Github tab. Click on the folder that says `put-your-code-here`.
2.  Find the button that says “Create new file” and click it.
3.  Name the file `test-file.txt`, add some text to it, and go to the bottom of the screen and press the “Commit new file” button.
4.  You have just edited your _remote_ repository! Now return to your Cloud9 workspace. Notice how your `test-file.txt` is not there.
5.  To pull down changes, run the following command.

```bash
git pull origin master  
```

After running this command, you will now see `test-file.txt`!

I know this brief introduction to Git may have been intimidating, but Git is an essential and powerful tool to know about and use. Although there are complex functions built into git, the basic process is as follows.

1.  Make local changes
2.  Commit local changes
3.  Push local commit to remote repository
4.  Go to bed.
5.  Wake up.
6.  Pull remote changes
7.  Repeat

Although you will surely run into issues in between these steps, this is the basic flow that you will use for the rest of your coding career!

What is a Coding Environment?
-----------------------------

In the previous section, we looked briefly at code in Cloud 9 which is called an IDE, or “Integrated Development Environment”. This IDE made things easy for us because the GCC compiler was already installed. Fortunately and unfortunately, there are three main operating systems out in the wild.

1.  Linux
2.  Mac
3.  Windows

All three of these operating systems run differently (although Mac and Linux are very similar), and use different compilers/assemblers to run code. What does this mean for you? Not a whole lot. What does it mean for me? It means that if I wanted to teach you how to code on your local machine, I would need to create three separate tutorials. For other crash courses, I will cover each of the operating systems, but since this crash course is on coding fundamentals, we will continue to use Cloud9. That said, I wanted to quickly cover the three main types of coding environments that you will find.

### Local Environment

You hear it all the time–local development, but what does it mean? A “local” environment simply means that you are using that physical machine sitting 2 feet in front of you for all your coding needs. When you are coding locally, you do not need an internet connection to run your programs.<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">There are exceptions to this, but let’s not confuse ourselves. You sometimes need to access the internet to connect to a database, download coding packages, or push/pull to and from Github. These subjects are more advanced and will not be covered in this crash course.<i class="fa fa-window-close close-it"></i></span></span> The local development environment is also one of the more difficult environments to setup if you are a beginner because it requires you to download software packages to certain locations on your filesystem and troubleshoot issues regarding these downloads.

### Hosted/VPS Environment

Believe it or not, Cloud9 is actually a virtual private server running behind the curtains. The user interface (UI) is very simple to use, but beneath the surface, you are actually reading and writing files from some computer sitting in a dark, cold room in West Virginia (okay, maybe not West Virginia). Luckily, all the hard work that goes into setting up such a machine has been done and you can get right to work!

In the future, you may need to use a virtual private server to host an application, run compute-intensive operations, or maybe even as a personal device if you are savvy enough. For now, I would not worry about understanding this. Future tutorials will take you through the complexities (there are lots of them!) related to a VPS.

### Virtual Machine/Containerized Environment

These environments are a bit more difficult to understand for a beginner, but I wanted to make you aware of their existence. Until you have spent hours and hours struggling to get your environments set up correctly, you will not appreciate the power of these. That said, a virtual machine is simply a computer running inside another computer. Why is this helpful? It is helpful because that computer running inside your physical computer can have an entirely different environment setup than your physical computer. Again, why is this helpful? When you are working on a coding project with multiple people, it is difficult to get everyone on the same page. For example, say you are working on a Python utility that transcribes videos. You might expect this to take several months and several developers to complete. Not only is the project a lofty task, but all of your developers need to have the same version of Python downloaded, the same Python dependencies downloaded, the same versions of these dependencies, and every time something is updated, all the developers must perform the same update. Wouldn’t it be nice if you could setup one computer, clone that computer, and distribute it to all the developers at once? That’s what a virtual machine is.

I debated whether to bring up the topic of “containerization”, but it is too important to leave out. Down the road, you will surely run into something called [Docker](https://www.docker.com/). Without getting into too much detail, Docker is a container-based utility that acts very similar to a virtual machine, but is quicker, less memory intensive, and more agile.

Again, do not spend too much time trying to understand virtualization and containerization. At this stage, it is not important to understand these, but it is important that you are aware that they exist.

Types of Coding
---------------

We are almost to the end! I wanted to quickly describe some of the common types of coding that you will see and how they all relate to the concepts you have just learned. I have split the types of coding into three buckets.

1.  Web / Application Developer
2.  Networking Engineer / Systems Engineer / Security Engineer
3.  Data Scientist / AI / Deep Learning
4.  Other

Rather than trying to explain the nuances between each of these engineers, I have given an example project that each might have built. I have often found that trying to explain the differences between types of coding and coding languages to a new programmer is not effective, so hopefully visual representations will help! If you have further questions, I suggest Googling each.

### Web / App Developer

Most of the tutorials you find on my blog and YouTube channel fall into this category. Of the listed types of engineers, these are the ones that probably write the most code on a daily basis. They are familiar with many languages (HTML, CSS, Javascript, Python, PHP, Java, Git, etc. etc.).

Examples: Facebook, Slack, Spotify, Twitter, Instagram

### Networking Engineer / Systems Engineer / Security Engineer

Example: There are no tangible examples of this. It is mostly behind the scenes work. To visualize, think of the team of engineers that set up a corporate office’s wifi network, makes sure that all the computers and phones are connected, make sure they are secure, and make sure the system is designed to last.

These engineers are the ones that make the stuff in the picture below connect and a LOT more that you should probably appreciate more than you do:

![](/blog/2018/how-to-code-ultimate-crash-course/networking.jpg)

### Data Scientist / AI / Deep Learning

These engineers work with giant datasets and quite literally make magic happen. You know those cars that drive themselves (cough Tesla)? These engineers are responsible for writing the code that allows these cars to drive themselves.

![](/blog/2018/how-to-code-ultimate-crash-course/tesla.jpg)

### Other

Although I would love to document all of the possible engineers that would write code, I simply do not have time. I believe I have covered the major categories, but there are plenty of other jobs that require you to write code. Furthermore, jobs that historically have not required coders (finance, real estate, etc.) are now demanding engineers who can write logic into their business processes.

Resources and Next steps
------------------------

As I promised, below are the resources that I recommend for you to learn how to write code. There are hundreds of other resources that could probably teach you the same thing, but I have only listed the ones that I feel will have the _greatest impact_ (and are free!) on you and are required to teach yourself coding. Follow the steps below (easier said than done!) and you _will_ learn how to code.

1.  Read [this article](https://www.bloomberg.com/graphics/2015-paul-ford-what-is-code/). It will give you the big picture and is quite entertaining.
2.  [Take the online CS50 course](https://courses.edx.org/courses/course-v1:HarvardX+CS50+X/course/) (spoiler: this is going to take a while, and it’s not easy!). Although this course gets into computer science topics that are not necessary to writing code in more modern languages like Python, I believe they are important for anyone who codes to understand. This is Harvard’s introductory computer science course and it is absolutely free.
3.  Start building - This is by far the hardest step. I cannot tell you what to build or what type of engineer to become. You must search and search and search some more. Whatever you choose, the most important thing is to just START. Apply for a job. Build an app. Contribute to an open source project. Teach your friends how to code. Participate in a meetup. Go to a [hackathon](https://hackathon.guide/) (my personal favorite way to learn). Doing any of these will get the wheels turning and allow you to see some of the possibilities out there. Although it is tempting to continue taking online courses forever and ever, eventually, you will need to hunker down and build something. It will be hard, so just accept that fact and do it anyways!

Along the way, you will have many many questions. Instead of relying on other people to answer them, you MUST learn how to use Google. I spend more time on Google and StackOverflow reading through error logs and questions than I probably spend actually coding. To be honest, I do not even have many of the Python commands memorized. To write this tutorial, I even had to look up a few things to refresh my memory. Programming is not about memorizing commands (although some are useful), but rather learning how to Google. Before we part ways, let me give you a real-world example of what I mean. Just a few weeks ago, I was trying to send an email programmatically through my Wordpress site. I had my application all configured, but I had no idea where to start. This is how I solved that problem.

1.  I went to Google and typed "How to send email with NodeJS"<span class="tooltip"><i class="fa fa-question-circle tooltip-icon"></i><span class="tooltip-text display-none">NodeJS is a javascript framework that is common among web developers. Since I was using this framework for my project, I made sure to add it to the end of my Google search<i class="fa fa-window-close close-it"></i></span></span>
2.  I clicked on [this link](https://stackoverflow.com/questions/4113701/sending-emails-in-node-js) and found out that there is a software package called “Nodemailer” that will do the job.
3.  I then went to the [“Usage” section of the Nodemailer documentation](https://nodemailer.com/usage/) and learned how to install the Nodemailer software and use it.
4.  I was not impressed with Nodemailer’s documentation and was not able to figure out how to use the software, so I went back to Google and typed “How to use nodemailer”
5.  That Google search sent me to [this post](https://codeburst.io/sending-an-email-using-nodemailer-gmail-7cfa0712a799) which helped me solve my problem.

As you can see, programming is not necessarily about how much you know, but is about solving problems and looking up information quickly.

Conclusion
----------

Congratulations for making it to the end of this tutorial! At this point, if your head is not spinning, then you are blessed with exceptional intelligence. As stated in the beginning, you should not feel the need to fully understand everything that I have mentioned in this post. The goal of this tutorial was to _expose_ you to all the crucial concepts that you will need to eventually understand. Now that you have a basic overview of coding, you can start doing the actual work it takes to teach yourself coding. I wish I could implant my knowledge instantly into your head, but the reality is that you will need to spend hours and hours practicing before this entire tutorial seems like a breeze. That said, the journey of learning how to code is a super exciting one, and you will feel your world opening up the more you learn. Many people suggest learning Spanish, Chinese, or French in school, but the real language you should be fluent in is the language of Code.

Best of luck to you, and feel free to reach out with any questions!
