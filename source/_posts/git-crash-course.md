---
title: Git Crash Course
date: 2019-03-05 20:33:18
tags:
---

## Prerequisites

This course is for anyone who is [familiar with the command line](https://zachgoll.github.io/blog/2019/bash-essentials) and needs to know all the essential commands for the Git utility.  You will need to have the following knowledge:

* Basic understanding of coding languages (we will not write any code, but will be looking at some)
* Basic mastery of the command line
* Basic understanding of open source software

## Topics

* Why use Git?
* Installation, and adding your credentials to Git
* The differences between Github, Gitlab, Bitbucket, Local
* A Simple, Effective Workflow
* Common Git Problems and Solutions

## Why Use Git?

Git is a widely accepted, open source, local source control tool that enables single developers or teams manage their source code in a decentralized way.  I devoted an entire section to this question, but truthfully, you won't see the true power of this tool until we start using it.

## Installation and Adding Credentials to Git

To install, run the following command on: 

**Windows (using Chocolatey Package Manager)**

```bash 
choco install git -params '"/GitAndUnixToolsOnPath"'
```

**Mac (using Homebrew Package Manager)**

```bash 
brew install git 
```

**Linux (using Aptitude Package Manager)**

```bash 
sudo apt-get install git
```

Once installed, you will want to add your credentials with the following two commands.

```bash 
git config --global user.name "Name"
git config --global user.email "Email"
```

Also, set the settings related to line endings.  These settings are not 100% necessary, but a good default.  To learn more, you can read up with [this Stack Overflow post](https://stackoverflow.com/a/10855862/7437737).  In short, depending on which OS you are running on and which utility you use to commit files, the byte character used at the end of each line may be different.  It could be a legacy CR (carriage return), a LF (line feed), or CRLF.  This can cause issues in a repository because it may show that all files have been modified when they have not been.  Anyways... Here are the settings.

```bash
# Windows
git config --global core.autocrlf true
git config --global core.safecrlf true

# Mac / Linux / Subsystem for Linux
git config --global core.autocrlf input
git config --global core.safecrlf true
```

If you want to edit these in a file, you can open up `~/.gitconfig` in a text editor.  It will look something like this (notice the four lines we added).

```
[user]
    name = Zach Gollwitzer
    email = <your-email-here>
[core]
    editor = vim
    whitespace = off
    excludesfile = ~/.gitignore
        autocrlf = input
        safecrlf = true
[advice]
    statusuoption = false
[color]
    ui = true
[push]
```

The last thing you might want to do if you are setting up Git on a personal computer is authenticate with Github (assuming you are using Github as a source control host).  This will eliminate the need to type in your username and password every time you want to push or pull from a remote repository.  There is the option to add your Git password to your computer's credential store, but I am not showing that method due to the fact that you password is stored unencrypted.

First, check if you already have an SSH key on your computer named "id_rsa" (this is the one that Github accepts).  To do this, run the following command.

```bash
ls -la ~/.ssh | grep 'id_rsa'
```

If this command returns two entries or more (id_rsa and id_rsa.pub), then you **do not** need to create a new key.  If it does not return anything, then generate a new SSH key.  If you are on Windows, you will need to do this with Putty.  Otherwise, generate it with the following command.

```bash
cd ~ && ssh-keygen -t RSA
```

Once you have this key, login to Github and do the following.

1. Click on your Profile Settings
2. Click "SSH and GPG Keys" on the left-hand tab
3. Click "New SSH Key"
4. Give the key a unique name (I call mine "personal-pc")
5. Go back to the terminal, and print out the _public_ version of your key.  If you followed my instructions and called it "github", then you should have two keys saved in the `~/.ssh` folder--`id_rsa` and `id_rsa.pub`.  You want to copy the contents of `id_rsa.pub`.  Paste that key into the required field in your Github account.

At this point, Github has a record of the computer you are working from.  The last step is to make sure that your "remote origin" (more on this later) is correct for your local repository.  In other words, when you go to a repository, there should be two options in the "Clone or download" field: 

1. git@github.com:<username>/<repository-name>.git
2. https://github.com/<username>/<repository-name>.git

To avoid typing in your password for every push/pull to and from the repo, you will need to use **#1**.  If this does not make sense yet, just keep reading and come back to this section when it does.

## Barebones Basics

If you already know the basic Git terms and how to commit, push, and pull, then you can skip down to the section named [A Simple, Effective Git Workflow](#A Simple, Effective Git Workflow).  If you are completely new to Git, this section will teach you everything you need to know to successfully use Git.

1. Source Code - this is a fancy term for "all the code that belongs to a project in its original state"
2. Repository - A "repo" is another word for a bunch of source code.
3. Branch - An essential concept of Git in general.  Each repository can have multiple branches.  Each branch can have unique source code.
4. Remote - this word could mean a lot of things, but in the context of Git, it refers to the version of the repo that is sitting on a server somewhere (in most cases, it refers to the version of a repo which lives on Github's servers).
5. Local - this word could also mean a lot of things.  It refers to the version of the repo that is sitting directly in front of you on _your physical machine_.  It is literally the code that is written to the disk on the computer sitting in front of you.  One caveat to this is if you are running Git from a virtual machine.  In that case, your repo is being stored on a _remote_ virtual machine _and_ a remote server, but this does not really matter.  Just think of the "remote" as "Github" and "local" as "my computer".
6. Commit - This will make more sense in a moment, but it is the action of "saving" your changes with a "receipt of save".  This is different from saving a document to your computer because once you save that document, you cannot go back to the previous version before the save unless you had made another copy of the document.  In Git, you _can_ go back to the previous version of the save, which is referred to as "reverting to the previous commit".
7. Push - Once you have "saved" (committed) at least one time, you can push those changes to your remote repo.
8. Pull - This means that you are retrieving new changes from your remote repository and updating them in your local version of the repo.  You will see why this is useful when we start talking about multi-contributor code projects.
9. Clone - This means you are creating a "copy" of an entire repository.  There can be an unlimited number of repo copies stored on an unlimited number of local machines that all push their changes up to the remote repo (i.e. Github).
10. Origin - This refers to the HTTP URL or SSH identifier for a specific remote repository and is how we push/pull to and from our local/remote repos.

To better understand all of these terms, we are going to create a brand new repository on Github.  Be sure to refer to the definitions above throughout.

To do this, either sign in to your account or create a new account (it's free forever).  Once you have created an account, click on the + icon in the top right corner of your screen and select "New repository".  You will be taken to the following screen: 

{% asset_img create-repo.PNG %}

For the purposes of our tutorial, do not click the "Initialize this repository with a README".  We will manually do this.  Click "Create Repository".  You will be taken to the setup screen.

{% asset_img setup-repo.PNG %}

When you first create a repo, you are shown four different options for setting it up, but really, there are only two methods to setup your repo.

1. Clone it
2. Manually add the remote origin URL (or SSH in our case)

We will go through both processes.  Remember, you can have _multiple copies_ of a single remote repository.  Open your terminal, navigate to whatever directory you want to put this example repository in, and type the following command (replacing the appropriate information).  Make sure you select the SSH version of the link to take advantage of what we setup earlier in this tutorial:

```bash 
git clone git@github.com:zachgoll/basic-git-tutorial.git
cd basic-git-tutorial
```

This will create a new folder on your computer called `basic-git-tutorial`.  This folder has no files in it yet, but it does have a folder called `.git` which you can see by typing `ls -la`.  This folder will keep track of all your commits, branches, etc. as we add them.  Since we cloned the repo, the remote origin will already be setup, and we can check this by typing the following command.

```bash 
git remote -v

# origin	git@github.com:zachgoll/basic-git-tutorial.git (fetch)
# origin	git@github.com:zachgoll/basic-git-tutorial.git (push)
```

You should see two URLs or SSH URLs which represent the path to your remote repo.  We can also set this up manually with the second method.

```bash 
# Create a new directory for your repo 
mkdir git-tutorial-manual 

# Enter the new directory
cd git-tutorial-manual 

# Initialize your Git repository
git init

# Setup the remote origin 
git remote add origin git@github.com:zachgoll/basic-git-tutorial.git
```

Following the above steps will get you to the same spot as after cloning the repository.  You can confirm by running the `git remote -v` command and making sure you have this setup correctly.  Now that we have the repository setup, we will create a couple folders and files to work with.

```touch 
mkdir source-code
touch source-code/index.html 
touch README.md 
```

In the `index.html` file, put a simple HTML document.

```html 
<!-- index.html -->
<html>
    <head>
        <title>Basic Webpage</title>
    </head>
    <body>
        <h1>Hello World</h1>
    </body>
</html>
```

In the `README.md`, add any text you want.  Now, we are going to "stage" these files for a commit.  You can either add them individually, or all at once.

```bash 
# Method 1: Add each file individually 
git add source-code/index.html 
git add README.md

# Method 2: Add all files in current directory 
git add .
```

Now, run the following command: 

```bash
git status 
```

This should output the following: 

```
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   README.md
	new file:   source-code/index.html
```

Now, we will "commit" the files.  Once we commit the files, the current state of them will forever be stored in this Git repository.

```bash 
git commit -m "Add files"
```

```
[master (root-commit) 7c076fa] Add files
 2 files changed, 10 insertions(+)
 create mode 100644 README.md
 create mode 100644 source-code/index.html
```

## Github vs. Gitlab vs. Bitbucket vs. Local

I won't spend your precious time wasting away on this question, but thought it was necessary to clarify that Github, Gitlab, Bitbucket and others are simply "hosting" platforms for the Git source control tool.  In other words, they run the computers that store your code remotely. 

Git is an open source software, so you could also setup your own Git host on an AWS, Azure, DigitalOcean, etc. server and it would work the same as with Github.  If you are savvy enough, this is a great way to save some money when you need to build software with your team privately (most of these hosting services charge for private repos).

## A Simple, Effective Git Workflow

The purpose of this tutorial is to provide a simple, systematic workflow that a single developer (or small team) can use to develop production-ready software.  If you are working on a larger project with a complex codebase, this method will provide you with a general understanding of how to contribute, but will not be entirely sufficient.  Some repositories will utilize 5+ different branches to develop software including branches for features, hotfixes, a main development branch, release branches, and even Git submodules within the project.  We do not have the time or reason to get that complex, and therefore the method I will show you includes a workflow with only 3 branches.  You can see it below, but I suggest opening it and printing it out for reference. 

<a href="localhost:4000/blog/2019/git-crash-course/github-workflow-diagram.jpg"><div class="click-pic">{% asset_img github-workflow-diagram.jpg %}<div class="overlay-pic">Click to Expand</div></div></a>

In this workflow, we have three branches:

1. Master - This branch will have _production_ code only.  In other words, anything you push to the master branch better be free of bugs.
2. Develop - This branch will be the "live" version of your software.  If you are working on a team, this is the branch that developers will push to on a regular basis with new features.
3. Feature - This technically is not a single branch because there can be tens, or even hundreds of outstanding feature branches at a given moment depending on the team size.  Each feature branch represents a new chunk of code that will eventually be tested and added to the codebase.

The basic steps in this flow are as follows:

1. Create a new branch from the develop branch and call it something like "feature-<describe feature here, or give it an ID>".
2. Work on your feature, committing to this feature branch
3. Test your feature
4. Merge your feature into the develop branch
5. Delete your feature branch 
6. Once enough features have been added, prepare your release
7. When the release is tested and prepped, merge the develop branch into master
8. Tag the master branch commit to the correct version (i.e. v1.1)
9. Repeat

This will make more sense if we actually go through the steps of creating a repository, writing some "production" code (it will be far from it, but for the purpose of the example it will work), and releasing that code.  Along the way, you will learn the following topics:

* Creating branches
* Switching between branches
* Merging two branches
* Understanding upstream vs. downstream
* Tagging commits (for releases on master branch)

### Setting up the Repo

Let's first setup a new repo, add some files, commit the files, and push them "upstream" to our remote repo.  If anything here gets confusing, go back and the basics section of this post.

```bash 
git init 
git remote add origin git@github.com:zachgoll/basic-git-workflow.git

touch index.html
echo "<html><head><title>Simple Webpage</title></head><body><h1>Hello World</h1></body></html>" > index.html

touch README.md
echo "This repository will show you a basic git workflow for individuals or small teams" > README.md

git add index.html README.md

git commit -m "First commit"

git push origin master
```

Let's now add a License to this repository.  I will add an MIT license, which is common for open source projects.

```bash 
touch LICENSE
```

Copy the following text into `LICENSE`.

```
Copyright 2019 Zach Gollwitzer

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
```

Stage, commit, and push "upstream" to your remote repo.

```bash 
git add LICENSE
git commit -m "Add license to project"
git push origin master
```

### Tagging the first Production Release

After running those commands, we have the first version of our software added to the master branch of our repository.  I know it is extremely simple, but we will just go with it and call it our "production web app".  Since we committed to the master branch, we will need to tag this first commit as "v1.0".  First, we will see what tags already exist in our repository: 

```bash 
git tag
```

This should not return anything because we have not tagged anything yet.  To add a tag, we will run the following command.

```bash 
git tag -a v1.0 -m "Added first tag"
```

The `-a` stands for an "annotated tag", and `v1.0` is the actual tag.  With an annotated tag, we need to add a message to our tag.  We can then see the details of this tag by typing: 

```bash 
git show v1.0
```

This will show who made the tag, the date of the tag, and the message of the tag along with other information.  Let's push this up to Github.

```bash 
git push --tags origin master
```

Since we haven't added anything since our last push, we must use the `--tags` flag to only push the new tag.  This worked easily because we were on both the branch and the commit that we wanted to tag.  We will come back to this topic later with a bit more complicated example.

### Creating and working with branches

Now that we have a production release, we cannot continue to commit code to the master branch.  Code needs to be tested and reviewed before releasing to production, so it is best that we do all of our development on a new branch.  Let's first look at what branches are already in our repository.

```bash 
git branch --list

# * master
```

All you should see is the master branch.  To create a "develop" branch with the exact code that is in the master branch already, we will run the following command.

```bash 
git branch develop

# Now run the list command again
git branch --list 

# develop, * master
```

At this point, we have two branches (master, develop) and two commits.  Before things start getting convoluted in our heads, let's take a moment to visualize what is going on.  Type the following command to list out all the commits we have done.

```bash 
git log

# Could also run 
git log --pretty=oneline
```

This will print out the following: 

```
Author: Zach Gollwitzer <email hidden for confidentiality>
Date:   Mon Mar 11 17:12:28 2019 +0000

    Add license to project

commit 7087a7eeab6803e957c3d9468e9f7a17d5043a05
Author: Zach Gollwitzer <email hidden for confidentiality>
Date:   Mon Mar 11 17:11:49 2019 +0000

    First commit
```

The alternative "pretty" command will output: 

```
ccb5d8e8c35364f2c98fb9f380404697973e11e8 (HEAD -> master, tag: v1.0, origin/master, develop) Add license to project
7087a7eeab6803e957c3d9468e9f7a17d5043a05 First commit
```

Notice how in the second version of the command, we see `(HEAD -> master, tag: v1.0, origin/master, develop)`.  What does all this mean?  To answer that question, we need to understand what branches are and how they work.  Currently, our Git repository looks like this: 

{% asset_img git-branching-1.png %}

At its essence, Git is a tool that uses pointers to track "snapshots" of files.  In our diagram, the arrows represent where each pointer points to.  The two commits are abbreviated to just their first six characters.  You can see that the latest commit points to the previous commit, and both the develop and master branches both point to the most recent commit.  But what is the `HEAD` box represent?

Understanding what the `HEAD` pointer does is paramount to understanding Git.  The `HEAD` pointer will _always_ be pointing at something, and whatever it is pointing at is the snapshot that you are currently working in.  In this case, we see that `HEAD` is pointing at `master` which is pointing at the most recent commit.  Yes, we created the `develop` branch just a moment ago, but `HEAD` is still pointing at `master`, which means that any changes that we stage and commit will be on the `master` branch.  Let's switch to the develop branch because we do not want to make changes to our "production" release v1.0.

```bash 
git checkout develop
```

{% asset_img git-branching-2.png %}

As you can see in the diagram, this command has told the `HEAD` pointer to point at this new `develop` branch.  Now, anything we change in the repository will be updated on this develop branch.  Let's go ahead and update our HTML file with some CSS.

```html
<html>
    <head>
        <title>Simple Webpage</title>
        <style>
            
            body {
                font-family: monospace;
                color: navy;
                padding: 40px;
            }
            
            .header {
                font-weight: 500;
            }
            
        </style>
    </head>
    <body>
        <h1 class="header">Hello World</h1>
        <br>
        <p>Welcome to the Git Tutorial</p>
    </body>
</html>
```

Now, stage, commit, and push these changes.

```bash 
git add index.html
git commit -m "Add CSS to HTML"
git push origin develop
```

Notice how this time, we are pushing to the develop branch.  We now have a repository that looks like the following: 

{% asset_img git-branching-3.png %}

The `master` branch is now an entire commit _behind_ the `develop` branch, and if we wanted to do a second "release" to version 1.1, we would need to "fast forward" the master branch pointer to point at this new commit.

Let's make a couple more commits to our develop branch in preparation for our second production release.  First, we will break out the HTML and CSS into separate files and commit that change.  Edit the `index.html` file to look like the following: 

```html
<html>
    <head>
        <title>Simple Webpage</title>
        <link rel="stylesheet" href="./style.css" type="text/css" />
    </head>
    <body>
        <h1 class="header">Hello World</h1>
        <br>
        <p>Welcome to the Git Tutorial</p>
    </body>
</html>
```

And create a new file called `style.css` and add the following to it.

```css
body {
    font-family: monospace;
    color: navy;
    padding: 40px;
}

.header {
    font-weight: 500;
}
```

Finally, stage, commit, and push upstream.

```bash 
git add index.html style.css
git commit -m "Split HTML and CSS into two files"
git push origin develop
```

Our repository now looks like this: 

{% asset_img git-branching-5.png %}

Before we make any more changes to the `develop` branch, let's create a feature branch called `feat1` to work on adding some javascript to our HTML document.

First, confirm that you are on the `develop` branch:

```bash
git branch

# * develop
#  master
```

Once you have confirmed that, create your feature branch _from_ the `develop` branch.


```bash
# Create the branch
git branch feat1

# Set the HEAD pointer to point at this branch
git checkout feat1
```

Usually we would only be creating a new feature branch under the following circumstances: 

1. We are working on a team and multiple team members are simultaneously working on different features that are all based on the code from the `develop` branch
2. We are working alone and we know that we will need to make changes to the develop branch before the feature is done.

In this case, we will assume that this feature is super complex and will take us days to finish.  Let's do our first "day" of work by creating a javascript file and adding it to the HTML.

Edit `index.html`.

```html
<html>
    <head>
        <title>Simple Webpage</title>
        <link rel="stylesheet" href="./style.css" type="text/css" />
    </head>
    <body>
        <h1 class="header hidden" id="header-id">Hello World</h1>
        <br>
        <p>Welcome to the Git Tutorial</p>
    <script src="./script.js"></script>
    </body>
</html>
```

Create `script.js` and add the following: 

```javascript
// Wait for window to load
document.addEventListener("DOMContentLoaded", function(event) { 
  
  // Get reference to header object
  let myHeader = document.getElementById("header-id")
  
  // Wait 3 seconds, then display the header
  setTimeout(() => {
    myHeader.classList.remove("hidden");    
  }, 3000)
});
```

Finally, update the CSS file `style.css` to have a `hidden` class.

```css
body {
    font-family: monospace;
    color: navy;
    padding: 40px;
}

.header {
    font-weight: 500;
}

.hidden {
    display: none;
}
```

You are now ready to make your first commit to the `feat1` branch.

```bash 
git add index.html style.css script.js
```

Since we added several files, let's check to see what is in our staging area with the following command.

```bash 
git status
```

```
On branch feat1
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   index.html
        new file:   script.js
        modified:   style.css
```

It tells us that we are on branch `feat1` and we have 3 files in the staging area waiting to be committed.  Now, let's commit and push upstream.

```bash
git commit -m "Add javascript to code"
git push origin feat1
```

We shut the computer down for the day and pat ourselves on the back for writing some super super complex javascript.  The next day, we realize that there is no `.gitignore` in our repository!  A `.gitignore` file stored at the root of your repository will tell Git to ignore some files.  Maybe you created a little `todo-list.txt` file in your repository, but you do not want to track this in your repo.

Although an unlikely story, let's create that todo list and `.gitignore` and commit the `.gitignore` to our `develop` branch.

```bash 
git checkout develop

touch .gitignore

# Tells Git to ignore this todo list file
echo "todo-list.txt" > .gitignore

# Create the untracked file
touch todo-list.txt
echo "Task #1 - Learn Git" > todo-list.txt

# Add all files to staging
git add .

# See what is in staging
git status
```

You will notice when you run `git status` that the only file that Git has recognized is the `.gitignore`.  It did not recognize the new `todo-list.txt` file that we put there.  Let's commit and push upstream.

```bash
git commit -m "Add .gitignore file"
git push origin develop
```

### Merging Branches

Let's do a little recap here before moving forward.  Since last time we looked at our repo we have committed some javascript to our `feat1` branch and a `.gitignore` file to the `develop` branch.  This means that we now have two separate branches that have a parent equal to our latest commit.  Run the following command: 

```bash 
git log --oneline --decorate --graph --all
```

This will give you the following output for our repository (in color on your screen).

```
* 547a448 (HEAD -> develop, origin/develop) Add .gitignore file
| * 69bdc19 (origin/feat1, feat1) Add javascript to code
|/  
* a4879ce Split HTML and CSS into two files
* 682f2aa Add CSS to HTML
* ccb5d8e (tag: v1.0, origin/master, master) Add license to project
* 7087a7e First commit
```

You can already start to visualize what your repo looks like using this log command, but here is a cleaner view for us to visualize it with:

{% asset_img git-branching-6.png %}

I know this diagram is getting large and difficult to comprehend, so let's take it piece by piece.  

1. First, direct your attention to the `HEAD` box.  Since we are currently on the `develop` branch, our `HEAD` pointer is pointing at that branch.
2. Next, look at the three branches (master, develop, feat1).  Obviously, `master` is way behind, and if we ran `git checkout master` to switch back to it, we would see that many of the files we have created (CSS, javascript, .gitignore) would be gone.  The `feat1` and `develop` branches on the other hand are both the same length, but with _different content_.  If we look at the `develop` branch files, we will see a `.gitignore` file, but no javascript code.  If we look at the `feat1` branch files, we will see javascript code, but no `.gitignore` file.  What we have is a complete divergence.
3. Last, look at the individual commit boxes.  When you take out all the noise, your project is no more than a series of commits.

{% asset_img git-branching-7.png %}

At some point, those two diverging commits at the end will need to join, or "merge" together into one.  But how do we choose which one merges into which?

If you remember, the `develop` branch is the main working branch, so we will want to merge our `feat1` branch back into `develop`.  Since we do not have any conflicts between these two commits (i.e. the edited files are completely different in each commit), we can merge `feat1` back into `develop` fairly easily.  First, run the `git branch` command to make sure that you are on the `develop` branch.  Since we are merging _into_ the `develop` branch, we need to be on it.

```bash 
git branch

# * develop
#  feat1
#  master
```

Once we are sure we are on the `develop` branch, we can merge `feat1` into it.

```bash
# Merge feat1 branch into develop branch
git merge feat1
```

You will be prompted with a message.  Just type `:wq` to save and quit the message.  After doing this, you have merged `feat1` branch into `develop`!  You can confirm this by typing: 

```bash 
git branch --merged
```

You should see `master` in this list because _everything_ that is in `master` is also in `develop`.  You will see `feat1` there because after our merge, _everything_ in `feat1` is now also in `develop`.  Since this is the case, we can delete the `feat1` branch with the following command.

```bash
git branch -d feat1

# Deleted branch feat1 (was 69bdc19).
```

Finally, type that fancy logging command to see what our repo looks like again.

```bash 
git log --oneline --decorate --graph --all
```

You should see the following: 

```
*   2f7765d (HEAD -> develop) Merge branch 'feat1' into develop
|\  
| * 69bdc19 (origin/feat1) Add javascript to code
* | 547a448 (origin/develop) Add .gitignore file
|/  
* a4879ce Split HTML and CSS into two files
* 682f2aa Add CSS to HTML
* ccb5d8e (tag: v1.0, origin/master, master) Add license to project
* 7087a7e First commit
```

You can now see that `HEAD` is pointing at the `develop` branch, and we have eliminated the divergence!  Here is our updated diagram.

{% asset_img git-branching-8.png %}

The `feat1` branch is gone, and `HEAD` is now pointed at the most recent commit on the `develop` branch.  I think at this point, we are ready to do our second release!

Let's merge our `develop` branch into `master`, and then tag the latest commit on `master`.  If you are developing a complex project, this is the point where you would want to "bump" your version to the next version.

```bash
# Switch to master branch
git checkout master

# Merge develop into master
git merge develop
```

Here is the output I got after the merge command:

```
Updating ccb5d8e..2f7765d
Fast-forward
 .gitignore |  1 +
 index.html | 13 ++++++++++++-
 script.js  | 11 +++++++++++
 style.css  | 13 +++++++++++++
 4 files changed, 37 insertions(+), 1 deletion(-)
 create mode 100644 .gitignore
 create mode 100644 script.js
 create mode 100644 style.css
```

Notice how it says "Fast-forward" at the top and says that it is updating `ccb5d8e` to `2f7765d`.  This just means that Git has taken the `master` branch pointer and "fast forwarded" it from commit `ccb5d8e` to commit `2f7765d`, which is our latest commit.  Let's now tag the release.

```bash
git tag -a v1.1 -m "Added second release tag"
```

Since `HEAD` is pointed at `master` which is pointed at our latest commit, the tag will go on the latest commit on the master branch.  And finally, our diagram looks like this: 

{% asset_img git-branching-9.png %}

You are now ready to start working on the `develop` branch again for your next software release!

```bash
git checkout develop

# Do lots of work!
```

## Common Git Problems and Advanced Git

In this section, I will be walking through some of the most common problems you might run into with this workflow and other advanced topics.  Since doing everything in the terminal can get tedious at times, I will also be introducing some of Visual Studio Code's source control features that might help you with tricky problems.  That said, I will show the terminal version of each feature that VSCode covers so that you can be fully sufficient just in the terminal!