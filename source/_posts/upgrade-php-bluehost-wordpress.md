---
title: How to Check PHP Version and Upgrade to PHP 7 with Wordpress and Bluehost
date: 2018-07-25 06:26:55
tags:
---

# Checking PHP Version

First, to check your current PHP version, open up your FTP manager.  At the root of your site (`public_html`), upload a file with the following contents: 

```php
<?php phpinfo(); ?>
```

Name this file something obscure like `ajdkslvhckdf.php` so bots cannot pick it up.  Upload the file via FTP to the root of your site.  Now, navigate to `http://my-site/ajdkslvhckdf.php` and you should see a big box with a bunch of information regarding your site.

# Upgrading PHP Version

First, make sure that your plugins and themes work well with whatever PHP version you are moving to.  Use [PHP Compatibility Checker](https://wordpress.org/plugins/php-compatibility-checker/) to determine this.  Fix any conflicts, and then navigate to `cpanel` on your Bluehost hosting account.  Scroll down to the "programming" section and click "PHP Config".

{% asset_img php-cpanel.jpg %}

Now, just select the PHP version you want to upgrade to.  Go back to the link that you checked earlier `http://my-site/ajdkslvhckdf.php` and your version should have changed!