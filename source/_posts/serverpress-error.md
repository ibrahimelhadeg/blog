---
title: Serverpress Error (Mac) - It appears that you have another web server already running 
date: 2018-07-29 07:22:49
tags: debugging
---

Serverpress is a freemium application used for local Wordpress development.

By default, Mac OSX has an Apache server running in the background which conflicts with Serverpress by default.

To turn it off, run `sudo apachectl stop`.  Now, in your browser, type `localhost` and you should see the classic "This site cannot be reached" page.  To verify this, turn the Apache server back on with `sudo apachectl start`.  Again, visit `localhost` and you should see bold text that says "It Works!".

To install Serverpress correctly, you will need to turn _off_ the default Apache server and _then_ install.
