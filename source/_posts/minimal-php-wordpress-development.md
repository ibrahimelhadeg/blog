---
title: Wordpress Development for NodeJS developers (minimal PHP)
date: 2018-07-29 09:28:39
tags: wordpress
---

The intention of this post is threefold: 

1. Allow developers more comfortable with languages other than PHP the ability to develop robust Wordpress sites with minimal PHP involvement
2. Create a seamless workflow between local, staging, and production sites
3. Minimize the use of Wordpress plugins to keep your site clean and easy to develop on

No matter what your favorite development stack is, there will come a time where you have to face the reality that knowing Wordpress is an essential skill.  Unfortunately for us non-PHP developers, Wordpress (written in PHP) is the most popular CMS platform on the market, and end users do not care what the backend of their favorite blog looks like.  Thanks to this fact, we must become familiar enough to make essential changes to our sites.

# Step 1: Create a Staging Site 

I am using Bluehost, so if you are running a different hosting provider, follow the relevant instructions for creating a staging site.

## Connect to Filezilla FTP Client and Synchronize

First, login to your hosting provider (I use Bluehost) and retrieve the FTP credentials.  Usually, there is an `.xml` file with a importable FTP configuration.  Download that configuration file, and open Filezilla for Mac.

Click File->Import and upload the `.xml` configuration file.  Sometimes, FTP is a bit stubborn, so you may have to change the setting `Encryption` in your `Site Manager` to "Only use plain FTP (insecure)".

Once this is done, you need to synchronize to your local computer.  I have a folder in my `$HOME` directory called `Wordpress` which has all of my sites organized by directory.  Make a new directory and name it.

```
cd ~/Wordpress
mkdir my-site
```

Now go back to Filezilla, open `Site Manager`, click on your site connection, and then click the `Advanced` tab.  Under the `Default Local Directory` field, click "browse", and open the `my-site` folder you just created.  In the `Default Remote Directory` field, enter the folder that you want to synchronize with.  In most cases, it will be the `public_html/` folder of your hosting account.

In the Filezilla client, on the remote site file pane, enter the folder you are syncing with (`public_html/`), and highlight all of the files and folders.  Once they are all highlighted, right click one of them and click "Download".  This will download all of the files from your Bluehost server to the local directory you are syncing with.  Give this process some time.  Depending on the size of your hosted site, it may take upwards of several hours.

If you want to download files faster, in the cpanel of your Bluehost account, navigate to `File Manager` and click "Website Root".  You can then right click on files and folders and move/copy them to other areas of your hosted filesystem.  This is useful if cloning a website for staging purposes.

_Note: It is not required to download every single file from your server, because it will often include media files.  Remember, FTP is simply a file transfer protocol and you can sync as little or as many files as you want.  For example, if the only thing you needed to modify on your site was the `style.css` file, you could simply download that file and be done.  I download the entire site so that I have a backup of it on my local computer and so I can manage my git repository more seamlessly._

## Create a Staging Subdomain 

1. Create the Subdomain 

Go to Bluehost and click `Domains` and then in the secondary menu, click `subdomains`.  Now, in the dropdown, select `my-site`, and in the input box, type either `dev` or `staging`.  Finally, make sure that the home folder for the subdomain is the same as the name you prepended to your domain.  For example, `staging.my-site.com` will live in the `public_html/staging` folder.

2. Create the Subdomain Database 

Now, we need to copy the main database to a new staging database.  In the cpanel, click `Databases`.  Under the "Create a New Database" section, enter a name for you database.  It can be anything, but I recommend naming it whatever you named the subdomain.  Databases in Bluehost are prepended with your username, so the database will be called something like `username_staging`.

3. Give a User Privileges to use the Staging Database 

The next step is critical.  You must now give the same user that has access to the production database access to the staging database.  On the same page you just created the database from, scroll to the bottom to the section called "Add a User to a Database".  Select the user which is privileged on the production database and assign that user to the newly created staging database.

4. Copy the Database 

Go to `phpmyadmin` from `cpanel` and click on the production database for you site.  Click on the "Operations" tab, and enter in your database information as shown below.  Note that we already created the database, so all we need to do is copy it.

{% asset_img copy-database.jpg %}

5. Modify the Database

We must now change the URL in several places of this newly copied database. 

In the "SQL" tab, execute the following four queries.  Make sure to replace the URL below with your appropriate URL.  Also, you may have to change the table names from `wp_options`, `wp_postmeta`, etc. to your custom prefix.  In my case, I have to change the tables to `wp_gurp_posts`. 

```SQL 
UPDATE wp_options SET option_value = REPLACE(option_value, 'ORIGINAL_URL', 'NEW_URL');

UPDATE wp_postmeta SET meta_value = REPLACE(meta_value, 'ORIGINAL_URL', 'NEW_URL');

UPDATE wp_posts SET guid = REPLACE(guid, 'ORIGINAL_URL', 'NEW_URL');

UPDATE wp_posts SET post_content = REPLACE(post_content, 'ORIGINAL_URL', 'NEW_URL');
```

6. Setup FTP Client for Staging Site and Install Wordpress

Navigate to wordpress.org and download the latest version of Wordpress as a zipfile.  Since we setup the FTP client earlier, I won't go into the process, but you will need to setup a new FTP account for the staging site.  From `cpanel`, click `ftp` and add a new account called `staging@my-site.com`.  Set it to the `/home/username/public_html/staging` folder, and click "create".

Download the ftp config file and import it into Filezilla.  Once connected via FTP, drag and drop the wordpress download (first unzip it) into the `staging` folder of your remote site.

7. Copy wp-content 

Finally, go to `cpanel` once again and find the link that says "File Manager".  Navigate to your production site folder, and then `wp-content` folder.  From here, we need to copy `themes/`, `uploads/`, and `plugins/` to the staging site filesystem.  Right click on each of these folders, click "copy", and then enter `/public_html/staging/wp-content/` as the path to copy to.

Note that with Bluehost, you will most likely not receive any status bar showing the files copying.  Just wait and be patient, and the files should show up in your staging site's `wp-content` directory.

8. Setup the Database to the Staging Site 

You now have a staging environment to work with.  Navigate to `staging.my-site.com` and you will be prompted by Wordpress to enter some information about your database.  Be sure to enter `localhost` for the host, `username_staging` as your database name (or whatever you used), and then the same username and password you use for the production database.  If you cannot remember this information, just open `wp_config.php` file from your production site, and the username and password should be near the top.

9. Disable SEO Plugins and Limit Site Access 

In the WP Admin dashboard, make sure that your SEO plugins or XML sitemap plugins are disabled.  You do not want Google indexing this site.

Also, in the admin dashboard, go to Settings->Reading and click "Discourage search engines from indexing this site".

Finally, download [restricted site access](https://wordpress.org/plugins/restricted-site-access/) to your site and whitelist the IP addresses you want to have access to the staging site in the settings.

10. Done! 

You should now be connected and be able to navigate to `staging.my-site.com` and see an exact clone of your production site.  This is the site that we will now be setting up to push all local changes to.

# Step 2: Export your main site 

Navigate to your main site's admin dashboard and download a plugin called [All in One WP Migration](https://wordpress.org/plugins/all-in-one-wp-migration/).  Enter the current site that you are exporting and the URL of the site you are cloning to, and then download as a "file".

{% asset_img migration.jpg %}

Now, navigate to your staging site's admin dashboard, download the same plugin, and go to the plugin page.  