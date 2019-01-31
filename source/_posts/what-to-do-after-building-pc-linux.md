---
title: What to do after building a Custom PC (Linux with Windows VM)
date: 2019-01-23 06:13:50
tags: hardware
---

If you are coming from YouTube, be sure to [check out my channel!](https://www.youtube.com/channel/UCDwIw3MiPJXu5SavbZ3_a2A/videos)  I recommend starting with my [operating systems video](https://www.youtube.com/watch?v=09puF-VKWeI).

_Note: This tutorial is specifically created for [How To Build A Video Editing Computer | $850 Ryzen PC Beginners Build Guide | 2017](https://www.youtube.com/watch?v=7-s04b5zel8&t=3s) video, but also has information on how to run the Linux driver for the [ANEWKODI wireless adapter](https://amzn.to/2Mpn9Kb)._

## Step 1: Create Bootable Linux USB Stick

1. First, go to the [Ubuntu website and download the latest Long Term Support Version](https://www.ubuntu.com/download/desktop) (18.04 at time of writing).  
2. Find a USB drive with more than 4GB of memory on it, and plug it into your Mac (if you have Windows, go through [this guide](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows) and return to step 3 here.).  Open Disk Utility on the Mac (press CMD + Space and type "Disk Utility"), find the USB stick, and click "Erase" (this will delete all files on it, so back up accordingly!).  Select the following options: 

{% asset_img partition.jpg %}

3. Convert the ISO download to an img file.  This will allow your new PC to recognize it as a bootable disk ([see this explanation](https://superuser.com/a/429233) for more detail).

```bash
# I am using the hdiutil tool to convert the ISO to an IMG format.  I first pass in the name and location # of the file I want to create (here I just kept the same name as the ISO without the extension), and 
# then the original ISO you want to convert.  If you are on Mac like me, this will create a dmg file, but
# this is actually an img file under the surface.
hdiutil convert -format UDRW -o ~/Downloads/ubuntu-18.04.1-desktop-amd64 ~/Downloads/ubuntu-18.04.1-desktop-amd64.iso
```

4. Unmount the USB from your Mac 

Before we can prep this USB for the new PC, we need to unmount it.  To do this, you need to find the disk number of the USB.  Run the following command.

```bash 
diskutil list 
```

You can see that here is my USB, and it is disk3.

```
/dev/disk3 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     Apple_partition_scheme                        *31.0 GB    disk3
   1:        Apple_partition_map                         32.3 KB    disk3s1
   2:                  Apple_HFS Ubuntu                  30.8 GB    disk3s3
```

Once you know which disk the USB is, run the following command, inserting the appropriate number.

```
diskutil unmountDisk /dev/disk3
```

5. Format the Bootable USB 

You will now use the `dd` utility, which stands for "Data Definition" and is an old Unix based operating system tool for managing things like boot sectors, hard drives, etc.

```bash 
# MAKE SURE YOU HAVE SPECIFIED THE CORRECT DISK NUMBER THAT YOU USED IN STEP 4
sudo dd if=~/Downloads/ubuntu-18.04.1-desktop-amd64.dmg of=/dev/disk3 bs=1m
```

After a few minutes, you should see something like this pop up on your Mac: 

{% asset_img  error-disk.jpg %}

Do not click anything yet.  First, go to the terminal and type the following command (with your appropriate disk number).

```bash 
diskutil eject /dev/disk3 
```

Now, go back to the dialogue and press "Ignore".  After this, your USB is bootable!

## Step 2: Boot your new PC 

1. Connect an HDMI (or other visual) cable to the HDMI port in your **graphics card** (not the default HDMI port), and then to your external monitor.  Connect your mouse and keyboard via USB, and then press the power button.  If the computer does not turn on, there is likely something wrong with your build, and you will need to troubleshoot on Google to figure that part out.
2. When the monitor comes on, you will see something like this screen: 

{% asset_img select-boot-device.png %}

3. Plug in your bootable USB stick
4. Turn your computer completely off (press or hold the power button on the case), and turn it back on.  Immediately after turning it on, hit the delete key on your keyboard repeatedly until you enter the BIOS screen.  You should see this: 

{% asset_img initial-bios.JPG %}

5. Click on the tab that says "Boot", go to Boot Option #1, and select your USB drive.

{% asset_img boot-screen.JPG %}

6. Save and Exit, and you will now be booted into the operating system installed on your USB.
7. Ubuntu will load and you will see something like this: 

{% asset_img use-install-ubuntu.png %}

8. Click "Install Ubuntu", and follow the steps (just use all the default settings here)
9. Restart your computer, and you will come to this screen: 

{% asset_img after-install-ubuntu.JPG %}

10. Remove the USB drive, hold down the power to turn the computer off, and then press the power again to turn it back on.  The computer will first go to the ASROCK motherboard screen, and then will boot into Linux.

## Step 3 - Setting up your Wifi 

Before we can do much of anything, we need to get Wifi working.  If you have ethernet access, you can just plug that into your PC and you're good to go!  But if you are like me and your router is across the room, you will need to setup the [ANEWKODI wireless adapter](https://amzn.to/2Mpn9Kb).  Lucky for us (joke), our PC will not work right away with this little device.  If you open up your Wifi settings, you will see "No Wi-Fi Adapter Found".

To set this up, we need to use a little external software for the driver.

1. Get your big new PC close to your router and plug in the ethernet.  Don't worry, you don't need to work here for the rest of your life--just until we download the necessary tools we need to get that wireless adapter working!
2. Plug in your wireless adapter
3. Open a terminal, navigate to the Downloads folder, and type the following commands.

```bash 
cd ~/Downloads 

# Download necessary packages 
sudo apt-get update
sudo apt-get install build-essential 
sudo apt-get install git

# Clone the repo and enter into it
git clone https://github.com/gnab/rtl8812au
cd rtl8812au

# Compile the C files 
make

# Make the wifi adapter available
sudo insmod 8812au.ko
```

At this point, if you open your Linux Wifi settings, you should see all of the available networks you can connect to.  Just a few more cleanup steps.

4. Add this driver to the list of built in drivers and configure it so it rebuilds on kernel updates and reboots 

```bash 
sudo cp 8812au.ko /lib/modules/$(uname -r)/kernel/drivers/net/wireless
sudo depmod 

# Install dkms and build 
sudo apt-get install dkms

# Need to copy the source files to a directory called `8812au-4.2.2`
sudo mkdir /usr/src/8812au-4.2.2
sudo cp ./* /usr/src/8812au-4.2.2

# add, build, and install 
sudo dkms add -m 8812au -v 4.2.2
sudo dkms build -m 8812au -v 4.2.2
sudo dkms install -m 8812au -v 4.2.2

# Boot loader
echo 8812au | sudo tee -a /etc/modules 

# Clean up 
sudo dkms remove -m 8812au -v 4.2.2 --all 
```

Your Wifi is now setup!  You can walk that big PC back to your desk and connect wirelessly!

## Step 3 - Creating a Windows Virtual Machine

It is really hard to break away completely from Windows.  Someone is always sending you Word, PPT, and Excel documents, and it is just easiest if you have a way to edit them.  If you have a Windows license key, you will use it during this process.  If you are like me, you don't have one and do not intend to buy one anytime soon.  That is fine too, because [Windows still offers a great deal of functionality without a full license](https://www.howtogeek.com/244678/you-dont-need-a-product-key-to-install-and-use-windows-10/).

To create our virtual machine, we will be using an Oracle product called Virtual Box.  

1. Download Virtual Box [from here](https://www.virtualbox.org/wiki/Linux_Downloads)(select the 18.04 version at the top of the list)
2. [Download Windows 10 here](https://www.microsoft.com/en-us/software-download/windows10)(64-bit)
3. You will want to move the Windows ISO file to a more permanent location than the downloads folder.  I moved mine to the Desktop.
3. Open Virtual box and click the blue ribbon that says "new"
4. Name your VM (whatever you want), select "Microsoft Windows" for type, and select "Windows 10 (64-bit)" for version.
5. For memory size, select 4096
6. Click the default settings for virtual hard disk 
7. Once you have completed the preliminary steps, go into the settings for your VM, click the "Storage" tab, click the "Empty" storage device on the left, and click the button you see here below (this screenshot is from another VM I was creating so it shows Ubuntu, but you will see the same options).  Select the Windows 10 ISO file you downloaded earlier in step 2.

{% asset_img storage.jpg %}

8. Click the big green "Start" button to start your VM 
9. If you do not have a Windows 10 license, just click "I don't have a product key"
10. Select "Windows 10 Home" edition 
11. IMPORTANT!!! - You will want to select the CUSTOM option for the Windows install which will use the virtual hard disk you created in step 6.  Since we are running Linux on our main machine, the first option is not going to work.
12. Be patient as Microsoft installs and follow the instructions.
13. When loaded, we need to setup a file share so that we can access files from Linux on Windows 10 and vice-versa.  First, we need to install Windows Guest Additions.  This is surprisingly hard to find, so [here is the link](download.virtualbox.org/virtualbox/5.2.24/) to the latest long term support version.  On this page, you will need to download the "ISO" version.
14. Click on the "Devices" tab at the top of your Virtual Box window, click "Optical Drives", and then click "Choose disk image".  Now find the ISO file you just downloaded and select it.
15. Click on the Devices tab again, and click "Insert Guest Additions CD image".
16. Now click into the Virtual Box window and open the file system.  Click "This PC", and double click on the Guest Additions.  You should see a bunch of files.  Double click the one without any extension to start the Guest Additions installer.
17. After a quick reboot, Guest Additions will be turned on.  The last step is to create a shared folder on your linux machine.  Get out of the Virtual Box window and open your host machine's (Linux) terminal.  Create a share directory.

```bash 
mkdir ~/vmshare 
```

18. Now, open your Windows VM settings, click "Shared folders", and create a new shared folder.  Select "Auto-mount" and for the folder path select the `vmshare` directory we just created.  Finally, select "make permanent".  Now, when you open your Windows file explorer, you should see a new drive (mine is the E: drive) where you can save files.  Save a file to this folder, and then check to see if you can find it on your Linux host machine at the directory path `~/vmshare`.

And that's it!  You are up and running with your newly built custom PC.  Customize it however you like!