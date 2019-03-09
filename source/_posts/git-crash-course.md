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

There are a few terms that you just have to memorize when it comes to Git for any of this post to make sense.  We will dive into each of them later, but for now, just know the following: 

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

Of course there are more terms to learn, but if you can make yourself familiar with these 10, the rest of the tutorial will make a bit more sense. 

## Github vs. Gitlab vs. Bitbucket vs. Local

I won't spend your precious time wasting away on this question, but thought it was necessary to clarify that Github, Gitlab, Bitbucket and others are simply "hosting" platforms for the Git source control tool.  In other words, they run the computers that store your code remotely. 

Git is an open source software, so you could also setup your own Git host on an AWS, Azure, DigitalOcean, etc. server and it would work the same as with Github.  If you are savvy enough, this is a great way to save some money when you need to build software with your team privately (most of these hosting services charge for private repos).

## A Simple, Effective Git Workflow

The purpose of this tutorial is to provide a simple, systematic workflow that a single developer (or small team) can use to develop production-ready software.  If you are working on a larger project with a complex codebase, this method will provide you with a general understanding of how to contribute, but will not be entirely sufficient.  Some repositories will utilize 5+ different branches to develop software including branches for features, hotfixes, a main development branch, release branches, and even Git submodules within the project.  We do not have the time or reason to get that complex, and therefore the method I will show you includes a workflow with only 3 branches.  You can see it below: 

