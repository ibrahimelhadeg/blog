---
title: Permission Denied Public Key Error Digital Ocean
date: 2018-11-22 18:12:36
tags: Coding, Digital Ocean 
---

I just recently switched over to [Digital Ocean](https://m.do.co/c/82d3943c33b4) for all my hosting needs.  Upon creating my first "Droplet" (their terminology for a VPS), I attempted to login to the VPS through the command line and received the following error.

```bash
root@<ip-address-here>: Permission denied (publickey).
```

To fix this, you need to understand a simple utility that comes with a Mac--the ssh tool.  This comes in several forms including `ssh`, `ssh-keygen`, `ssh-agent`, and `ssh-add`.  We will use all four of these at some point as shown below.

## Step 1 - Generate an SSH RSA Key 

Many services which utilize command line access (i.e. Github, hosting services) require you to provide a public RSA key that corresponds to your private key which is stored in `~/.ssh/`.  You can create a new SSH key for each service, or you can use the same one for all (not recommended).  To create a new key:

```bash
ssh-keygen -t rsa
```

```bash 
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/Zach/.ssh/id_rsa): digitalocean
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in digitalocean.
```

This will create an RSA protocol private and public keypair.  As the user, you will need to enter the name of the key and provide a local password for the key (if you don't want a local password, just press Enter twice).

## Step 2 - Load your environment 

The `ssh-agent` does not automatically load an environment for you.  Run the following command: 

```bash 
echo $SSH_AGENT_PID
```

Unless you have some special configuration, you will receive an empty value in the terminal.  Now, run the eval command (see `man eval` for more info) on the ssh-agent and echo out the same environment variable.

```bash 
eval `ssh-agent -s`
echo $SSH_AGENT_PID
```

This will now print out the process id of the ssh-agent.

## Step 3 - Add your SSH Key to the Agent 

If you skip this step, you will most likely receive the "denied (publickey)" error.  To add your most recent key, run the command below (I named this key "digitalocean" in step 1).

```bash
ssh-add /Users/Zach/.ssh/digital-ocean

Identity added: /Users/Zach/.ssh/digital-ocean
```

## Step 4 - Login to your VPS

Finally, we can use the plain `ssh` utility to remote in to our VPS.  Just run the following command.

```bash 
ssh root@<your-vps-ip-address>
```

## Step 5 - Configure your User 

It is smart to create a non-root user with administrator privileges once you are logged in to the VPS.  Perform the following commands using the root login.

```bash 
# Add user
adduser zach 

# Give user admin privileges
usermod -aG sudo zach 

# Switch to the new user
su - zach 

# Create an ssh folder 
mkdir ~/.ssh 
chmod 700 ~/.ssh

# Create an authorized keys file 
touch ~/.ssh/authorized_keys
```

Now, open up a terminal on your _local_ machine and run the following command to copy your newly created public key to the clipboard.

```bash 
cat ~/.ssh/digital-ocean.pub | pbcopy 
```

Navigate back to your VPS session.

```bash 
# You have your public key copied to your clipboard.  Save it to an environment variable
MY_KEY="<paste your key here inside quotations>"

# Make sure you copied it correctly 
echo $MY_KEY 

# If you did, now echo this into the new file 
echo $MY_KEY > ~/.ssh/authorized_keys 

# Set permissions on this new file
chmod 600 ~/.ssh/authorized_keys

# Return to root user 
exit 

# Logout of the VPS 
logout
```

You should now be back to your _local_ computer terminal and should be able to login to your VPS as your new user.

```bash
ssh zach@<my-vps-ip-address>
```
