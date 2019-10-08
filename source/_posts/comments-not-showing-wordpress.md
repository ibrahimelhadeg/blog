---
title: Comments Turned On But Not Showing Wordpress Genesis
date: 2019-05-28 17:27:51
tags: wordpress
---

Skip to [the end](#If-Nothing-Else-Works) if you've already checked that your comments are turned on in your theme settings, Wordpress settings, and on the individual post that you are trying to see comments on.

1. Make sure in your Genesis Theme Settings your comments are enabled.

{% asset_img wp-comments-1.PNG %}

<br>

{% asset_img wp-comments-2.PNG %}

2. Make sure your Wordpress comments are turned on in your `Discussion Settings`.  Your settings should look similar to this: 

{% asset_img wp-comments-3.PNG %}

<br>

{% asset_img wp-comments-4.PNG %}

3. Make sure you have turned on your comments in the individual post 

{% asset_img wp-comments-5.PNG %}

## If Nothing Else Works

I know of 2 possible reasons: 

1. If you've got this checkbox checked in your `Discussion Settings`: 

{% asset_img wp-comments-6.PNG %}

This could create a situation where some of your posts are showing comments while others do not.  Took me 2 hours to realize that this was my problem!

2. Your theme may not have comments written into the page/post templates.  This means that the `comments_template()` ([link]((https://developer.wordpress.org/reference/functions/comments_template/))) hook is not present, or in the case of Genesis, the `genesis_do_comments()` hook is not present (if you are using Genesis, you will not have this issue unless you have manually edited the Genesis files, which you should not be doing!).

If this post solved your issue, give it a like or a comment!