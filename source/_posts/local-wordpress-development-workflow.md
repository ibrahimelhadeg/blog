---
title: My Local Wordpress Development Workflow
date: 2018-07-15 08:09:15
tags: wordpress
---

In this post, you will learn how to: 

* Synchronize your Wordpress Site on a Mac with Bluehost and Filezilla
* Create a Staging Site on Bluehost for Testing 
* Creating a private repository on Bitbucket 
* Creating a deployment pipeline

# Connect to Filezilla FTP Client and Synchronize

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

# Create a Staging Subdomain 

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

# Creating a Bitbucket Repository 

Github is my choice Git hosting service, but unfortunately, the fee for creating private repositories is too high for me and Wordpress sites have several files (e.g. `wp_config.php`) that cannot be uploaded to a public repository without risking the leakage of sensitive data.

Bitbucket is another Git hosting service that allows you to create free private repositories.

## Understanding Local vs. Staging vs. Production

Something that I found difficult to understand is the difference between my local site, staging site, and production site.

### Local Site 

This will be the site that you develop most in.  It will be hosted somewhere like `localhost:8080` and will be running in Docker containers (more on this later).  It will not be hooked up to your real database.  The only overlap between the local site and your production site is three folders: 

1. `wp-content/uploads`
2. `wp-content/themes`
3. `wp-content/plugins`

These three folders will be replicas of your production site.  Everything else is just a plain old Wordpress site.  Just think of these three folders as the _center_ of your entire Wordpress site.  The Wordpress package and all the files you don't understand are acting as _support_ for the uploads, themes, and plugins.  They provide an _environment_ for these to run in.

### Staging Site 

The staging site's main purpose is to act as a testing ground for your most recent developments.  Say you create a brand new plugin and are not sure if it works properly with everything yet.  Of course you will test it rigorously on your local site, but there are always "gotchas" when taking things to a live server.  The staging site is running on the same server your production site is running on, but from a different directory.

This site _is_ connected to the production database.  When we push changes to our Git repository from our local site, there will be an auto-deploy script that pushes those local files to the staging site.  The staging site will be running all the same files as the production site minus those same three folders we talked about earlier.  Additionally, some files are slightly modified to account for the subdomain name that the staging site runs at.

In summary, the staging site has the _environment_ of the production site, but the themes, plugins, and uploads of the local site.

### Production Site 

As you may have guessed, the production site is that which sits at the end of the pipeline.  You have created a new plugin in your local environment (`localhost:8080`), tested it locally, and made a new Git commit and push to the staging repo.  This repo has a webhook setup to detect new pushes, and every time a new push happens, it takes the new files and uploads via FTP to your staging site.

The staging site is now updated with your new plugin and you can do QA testing to make sure that it plays nice with other plugins and the database in a live environment. 

Finally, you decide that the plugin has been tested rigorously enough, and want to put it in production.  To do this, you will manually sync your staging site into your production site via FTP.  You do this to make sure that unexpected code does not get pushed to production.

## Managing Deployment Pipeline with Docker, FTPloy, and Git

1. First, download the [latest community edition (CE) of docker](https://www.docker.com/community-edition).
2. Create a Bitbucket account and create a new repository.
3. Create a free [FTPloy](http://ftploy.com) account, and link your Bitbucket account in the user settings.  4. Create a new project in FTPloy.  Select the Bitbucket repository that you just created, give it a name, and click the "Deploy Now" button off.  Enter the FTP credentials you created for your staging site.  For the "Server Path", just enter `/` because you are hooking the repository up to the root of the staging FTP account.
5. Create a new directory on your local machine for the repository.  For example, I use `$HOME/Wordpress/my-site`.  Change to the new directory, and setup your repository.

```bash
cd my-repo-directory
git init 
git remote add origin https://username@bitbucket.org/username/my-site.git
```

6. Copy the Wordpress folder that you downloaded earlier to the new directory (all the files in the folder, not the folder itself)
7. Create a new file called `docker-compose.yml`, and paste the following code into it.

```yml
version: "2"
services:
  my-wpdb:
    image: mariadb
    ports:
      - "8081:3306"
    environment:
      MYSQL_ROOT_PASSWORD: password
  my-wp:
    image: wordpress
    volumes:
      - ./:/var/www/html
    ports:
      - "8080:80"
    links:
      - my-wpdb:mysql
    environment:
      WORDPRESS_DB_PASSWORD: password
```

Don't worry about changing the passwords.  This will map the current directory that has all the Wordpress starter files to the `/var/www/html` directory of the Docker container running Wordpress and connect mariadb to that Wordpress container.  

8. The last thing to do before firing up the local site is copy the themes, uploads, and plugins to the local site.  Login to your staging FTP client in Filezilla and make sure that your Remote Site is the staging site, and the Local Site is the directory folder we just created.  Now in the Remote Site panel, click into `wp-content/` and highlight the `themes/`, `plugins/`, and `uploads/`.  Right click and press "Download".  Choose the option that says "overwrite" so that the theme and plugin files from your staging site (which mirrors your productions site) will overwrite the defaults from the Wordpress install.

9. Finally, from the root of your local directory, run the command: 

```bash
docker-compose up -d 
```

This will start the database and Wordpress Docker containers in the background, and in a few seconds, you should be able to navigate to `localhost:8080` and see the 5-minute Wordpress install page.

10. Go through the 5 minute install.
11. Run the following command: 

```bash 
docker-compose down
```

This will stop the running containers and demonstrates how to shut down your Docker environment after a days work. 

At this point, we have a local site working, but there is one last step.  Before pushing to the Bitbucket repository (which will subsequently deploy to your staging site), we need to setup a `.gitignore` file.  Remember, we do not want the staging site to have the same Wordpress environment as the local site (i.e. `wp_config.php` will be different since there are different database connections).  To help us achieve this, create a new file called `.gitignore` in the root of your project, and add the following contents to it.

```
# -----------------------------------------------------------------
# .gitignore for WordPress
# Bare Minimum Git
# http://ironco.de/bare-minimum-git/
# ver 20150227
#
# This file is tailored for a WordPress project 
# using the default directory structure
#
# This file specifies intentionally untracked files to ignore
# http://git-scm.com/docs/gitignore
#
# NOTES:
# The purpose of gitignore files is to ensure that certain files not
# tracked by Git remain untracked.
#
# To ignore uncommitted changes in a file that is already tracked,
# use `git update-index --assume-unchanged`.
#
# To stop tracking a file that is currently tracked,
# use `git rm --cached`
#
# Change Log:
# 20150227 Ignore hello.php plugin. props @damienfa
# 20150227 Change theme ignore to wildcard twenty*. props @Z33
# 20140606 Add .editorconfig as a tracked file
# 20140404 Ignore database, compiled, and packaged files
# 20140404 Header Information Updated
# 20140402 Initially Published
#
# -----------------------------------------------------------------

# ignore everything in the root except the "wp-content" directory.
/*
!wp-content/

# ignore all files starting with .
.*

# track this file .gitignore (i.e. do NOT ignore it)
!.gitignore

# track .editorconfig file (i.e. do NOT ignore it)
!.editorconfig

# track readme.md in the root (i.e. do NOT ignore it)
!readme.md

# ignore all files that start with ~
~*

# ignore OS generated files
ehthumbs.db
Thumbs.db

# ignore Editor files
*.vscode
.vscode/

# ignore log files and databases
*.log
*.sql
*.sqlite

# ignore compiled files
*.com
*.class
*.dll
*.exe
*.o
*.so

# ignore packaged files
*.7z
*.dmg
*.gz
*.iso
*.jar
*.rar
*.tar
*.zip

# ignore everything in the "wp-content" directory, except:
# "plugins" directory
# "themes" directory
wp-content/*
!wp-content/plugins/
!wp-content/themes/

# ignore specific themes (default wordpress themes)
wp-content/themes/twenty*/

# ignore node/grunt dependency directories
node_modules/
```

I know it seems like a lot, but all this file does is ignores everything but the `wp-content/themes` and `wp-content/plugins`.  This means that the only files we will be updating in the remote repository are theme files and plugin files.  If you are advanced and are modifying other files on your site, you can adjust the file accordingly.

12. We now have a basic site to push to Bitbucket.  Let's do that now. 

```bash 
git add . 
git commit -m "Add site files"
git push origin master 
```

This should have pushed all your files to the Bitbucket remote repository, and FTPloy should have subsequently updated the staging site with any changes you made (we haven't made any).

You now have a basic setup for developing your Wordpress site.  Remember, the development process is as follows: 

1. Go to local directory and execute the command `docker-compose up -d`
2. Make your code changes to your local site and test/verify at `localhost:8080`
3. Commit and push your finalized changes to Bitbucket 
4. Test your site at `staging.my-site.com`.
5. When all tests complete, manually merge your staging site into production.

