---
title: How to Host Multiple Wordpress Sites on One Digital Ocean Droplet
date: 2018-11-26 08:18:58
tags: hosting
---

In order to host multiple sites on one [Digital Ocean Droplet(https://m.do.co/c/82d3943c33b4), you first need to understand a little bit about DNS (domain name system).

## Quick Primer on DNS 

There are three concepts to understand in relation to DNS.

1. Domain name registrar 
2. Nameserver 
3. A, AAAA, and CNAME records

### Domain Name Registrar 

There are hundreds of domain name registrars which are companies that provide the service of registering domain names with [ICANN](https://www.icann.org/).  These registrars act as directories for domain name lookups.  For example, my personal site, `thediygolfer.com` is registered through NameSilo.  When I type `www.thediygolfer.com` into my browser, it will retrieve a single piece of information from my registrar--the nameservers that this domain points to.

### Nameserver(s)

Hosting companies (like Digital Ocean) operate nameservers, which are simply computers that store information about a specific domain.  If you type `whois thediygolfer.com` into your terminal, you will find a bunch of information about this domain including the address of the registrar and the nameserver addresses.  Although these nameservers provide various functionalities in the DNS world, we are concerned about two primary functions.

1. What IP address my domain name points to 
2. What subdomain rules are setup on the nameserver about this particular domain

### A, AAAA, and CNAME records

These are the 3 most common pieces of information that sit on a nameserver like Digital Ocean's nameservers.  When I login to my NameSilo account and direct my domain name `thediygolfer.com` to the Digital Ocean nameservers, these nameservers will map `thediygolfer.com` with a specific droplet that I am running (simply an IP address).  This is the most basic "A record".  It points `thediygolfer.com -> <ip-address>`.  The AAAA record is the IPV6 version, so instead of pointing my domain at an IPV4 address, it points it at an IPV6 address (same functionality and result).  The CNAME record will point one domain name (thediygolfer.com) to another domain name (thediygolfernewsite.com).  For hosting multiple Wordpress sites on one Digital Ocean droplet this is not necessary.

The important thing to understand when setting up multiple Wordpress sites is whether you are attempting to host two _primary domain_ sites, or one primary domain and one _subdomain_.  In this tutorial, we will be hosting one _primary domain_ and one _subdomain_ for our main site and our staging site (where development and testing happens).  In order to set this up, we need to create the following A records in Digital Ocean.

```
thediygolfer.com.	    14400	IN	A	<droplet-ip-address>
dev.thediygolfer.com.   14400   IN  A   <same-droplet-ip-address as above> 
```

The above A records will appear if you type `dig thediygolfer.com` into your Mac terminal.  Notice the period at the end of each domain and subdomain.  These records will sit on Digital Ocean's nameservers and tell anyone trying to reach either of these domains that they both sit at the designated IP address.

#### Setting DNS records in Digital Ocean 

1. Click the Networking tab in your dashboard 
2. Enter your domain name (i.e. `thediygolfer.com`)
3. Click on your new domain name (it will have 3 initial DNS records that all point thediygolfer.com to `ns1.digitalocean.com`, `ns2.digitalocean.com`, and `ns3.digitalocean.com`)
4. Make sure you have selected the "A" record type, and then enter a "@" symbol in the HOSTNAME field, select your droplet, and click "Create Record".  This represents our first A record as shown in the previous section.
5. Again, select the "A" record type, and then enter "dev" in the HOSTNAME field, select the _same_ droplet you just chose, and click "Create Record".  This represents the second A record as shown in the previous section.
6. You are now done configuring the Digital Ocean nameserver!

But how could two separate domains point to the same exact IP address yet host two different Wordpress sites??  Virtual Hosts.

## Virtual Hosts

Since web traffic from `thediygolfer.com` _and_ `dev.thediygolfer.com` is all coming to the same Droplet (ip-address), we need to configure some settings within our droplet.  To do this, we first need to install and adjust some permissions with the steps below.  Before doing the following steps, make sure you:

1. Have created the `WordPress on 18.04` (or whatever the current version is) "One-click apps" droplet.
2. Have [created a non-root user with admin privileges](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04)
3. Logged in with this user via ssh

```bash
# Updates packages and installs the Apache2 server we will use for our Wordpress 
sudo apt-get update
sudo apt-get install apache2

# Create the directories where our site files will be located
sudo mkdir -p /var/www/thediygolfer.com/public_html
sudo mkdir -p /var/www/dev.thediygolfer.com/public_html

# Allow the current user to modify files in these site directories
sudo chown -R $USER:$USER /var/www/thediygolfer.com/public_html
sudo chown -R $USER:$USER /var/www/dev.thediygolfer.com/public_html

# Set global permissions 
sudo chmod -R 755 /var/www 

# Create the homepage for the main site (will eventually replace with Wordpress site files)
touch /var/www/thediygolfer.com/public_html/index.html
echo "<html><head></head><body><h1>thediygolfer.com is working!</h1></body></html>" > /var/www/thediygolfer.com/public_html/index.html

# Create a homepage for the development site (will eventually replace with Wordpress site files)
touch /var/www/dev.thediygolfer.com/public_html/index.html
echo "<html><head></head><body><h1>dev.thediygolfer.com is working!</h1></body></html>" > /var/www/dev.thediygolfer.com/public_html/index.html
```

At this point, we have two site directories with super simple home pages.  If you had permissioning errors with any of the above commands or any of the commands to follow, just run the `sudo chown -R $USER:$USER /directory/to/the/file.ext` to enable root privileges to edit.  Now, we need to create the two virtual hosts so that when web traffic comes into our droplet, they are directed to the site based on the original web address.

```bash
sudo chown -R $USER:$USER /etc/apache2/sites-available

# Create the virtual host files 
touch /etc/apache2/sites-available/thediygolfer.com.conf
touch /etc/apache2/sites-available/dev.thediygolfer.com.conf

# Disable the old virtual host default file 
sudo a2dissite 000-default.conf
```

The default host file looks like so: 

```bash 
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        <Directory /var/www/html/>
            Options FollowSymLinks
            AllowOverride All
            Require all granted
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Without getting into too much detail, when traffic comes through port 80 (the default HTTP port), it will be redirected to the `DocumentRoot` of `/var/www/html` which by default (if you are using the Wordpress One Click Application) will be the Wordpress installation.  What we want to do is redirect traffic based on the host.  Below is the proper configuration that we will need.  Notice we have added a `ServerName` and `ServerAlias` field and pointed these hosts to the new document root that we placed the simple `index.html` file in.   We have also removed the Directory field as it is unecessary.

```bash
<VirtualHost *:80>
    ServerAdmin zach@thediygolfer.com
    DocumentRoot /var/www/thediygolfer.com/public_html
    ServerName thediygolfer.com
    ServerAlias www.thediygolfer.com
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    <Directory /var/www/dthediygolfer.com/public_html/>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

You will need to edit the `/etc/apache2/sites-available/thediygolfer.com.conf` file and the `/etc/apache2/sites-available/dev.thediygolfer.com.conf` file to have the information shown above.  Make sure to replace the `ServerAdmin`, `DocumentRoot`, `ServerName`, and `ServerAlias` with the appropriate information.  Also, make sure under the <Directory>, you have AllowOverride set to "All".  This will allow our `.htaccess` file to work.  After completing this, we need to activate these two new host files and restart the Apache2 server.

```bash
# Enable the hosts
sudo a2ensite thediygolfer.com.conf 
sudo a2ensite dev.thediygolfer.com.conf

# Restart Apache2
sudo systemctl restart apache2 
```

## Testing the configuration

There are 2 ways you can test the configuration.

1. Locally
2. With a free .tk domain 

### Local Testing 

To test the configuration locally, make sure you are **on your local machine** (not ssh'd into the VPS), and open the `/etc/hosts` file.  If this file doesn't yet exist, create it.

```bash 
# /etc/hosts file 

<your-digital-ocean-droplet-ip-address>     thediygolfer.com 
<your-digital-ocean-droplet-ip-address>     dev.thediygolfer.com 
```

By doing this, you are telling your local laptop that all traffic coming to `www.thediygolfer.com` and `dev.thediygolfer.com` should be redirected to the IP address of the Digital Ocean droplet.  This will only work on your local computer and is not a live change.

### .tk domain 

You can get a free [.tk domain](http://www.dot.tk/en/index.html?lang=en) and use it for testing.  If you choose this method, you will need to modify a few things.

1. In your `.conf` files, you will need to update the `ServerName` and `ServerAlias` to the domain name you have obtained for testing.
2. Point your new domain at the Digital Ocean nameservers (`ns1.digitalocean.com`, etc.)
    * Create an account at dot.tk, and then navigate to https://my.freenom.com/clientarea.php?action=domains
    * On your domain, click "Manage Domain"
    * Click "Management Tools"
    * "Use Custom Nameservers" and enter the Digital Ocean servers 
3. Create a new domain in your Digital Ocean Dashboard and add the A records we added earlier in this post

So for example, if I got `thediygolfer.tk` domain name, I need to add this to all my `.conf` files, point it to the Digital Ocean nameservers, and then create the following A records in Digital Ocean DNS:

```bash
thediygolfer.tk.	    14400	IN	A	<droplet-ip-address>
dev.thediygolfer.tk.    14400    IN   A    <same-droplet-ip-address as above>
```

If all goes smoothly, when you navigate to `http://www.thediygolfer.tk` in the browser, you should see a blank page that says "thediygolfer.com is working!" and when you navigate to `http://dev.thediygolfer.tk`, you should see a blank page that says "dev.thediygolfer.com is working!".  These are the `index.html` pages that we placed in the `/var/www/thediygolfer.com/public_html` and `/var/www/dev.thediygolfer.com/public_html`.  

## Extras

A few tutorials for extra functionality.

### Hosting a MEAN Stack Application alongside Wordpress sites 

In my personal situation I needed to run two Wordpress sites (live and development) and a simple MEAN stack application alongside it.  To do this, log in to the VPS and follow these steps.

```bash 
# Update packages
sudo apt update 

# Install essential packages 
sudo apt install nodejs 
sudo apt install npm 

# Install nvm (node version manager)
# Get the latest version at - https://github.com/creationix/nvm/blob/master/README.md
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash

# Activate nvm 
command -v nvm

############################################# 
# Logout and log back in to the ssh session 
#############################################
# New Session
#############################################

# Check to make sure nvm is working 
nvm --version  # 0.33.11 

# See the current versions available
nvm ls 

# Install the version that you desire 
nvm install 8.10.0

# Switch to the newly installed version 
nvm use 8.10.0

# Check to make sure it all worked 
node -v        # v8.10.0

# Clone your Node application to the home directory of your VPS
cd ~/
git clone <your repository here>

# Install application dependencies
npm install 

# Install Angular dependencies 
cd /<your-angular-app-directory>
npm install

# Install the Angular Client globally
sudo npm install -g @angular/cli

# Build your Angular application (mine builds to the public/ directory in the root of my project)
ng build --prod
```

At this point, you can test run your application to make sure it works, but it will not show up when you arrive at the IP address of the server.  Like above, I will need to create a folder in the `/var/www/` directory.  This NodeJS app is a training portal for users of the site, so I want to call it `/var/www/training.thediygolfer.com`.  Create that directory, create a new host file in `/etc/apache2/sites-available/training.thediygolfer.com.conf`, and replace the hosts file with the appropriate information.  Since my Node application serves HTML content from the `<app-root>/public/` directory, I will actually need to update my `.conf` file so that the `DocumentRoot` property has the path of `/var/www/training.thediygolfer.com/public_html/public` rather than `/var/www/training.thediygolfer.com/public_html`.

The fact that I placed my application in the `/home/zachgoll/` directory is a design choice and personal preference.  I do not like managing my application in the `/var/www/` directory, and the makers of UNIX did not intend for this directory to be a development directory.  To avoid this, I need to create a symlink in the `/var/www/training.thediygolfer.com/public_html` directory to point to the actual application.

```bash 
# Notice the asterisk at the end of my app.  I want to place all the files in the public_html/ directory so I need to do this
sudo ln -s /home/zachgoll/training-app/* /var/www/training.thediygolfer.com/public_html/
```

If you did this correctly, when you run `ls /var/www/training.thediygolfer.com/public_html/`, you should see all your site files along with the original `index.html` that we created earlier.  At this point, go ahead and delete that index.html file.  Now, every time you rebuild the application to the `/home/zachgoll/training-application/public/` folder, it will be reflected in `/var/www/training.thediygolfer.com/public_html/public/` folder and will be displayed on the virtual host.

If you need to unlink a symlink (maybe you messed it up), just navigate to the directory it exists in (in this case it is /var/www/training.thediygolfer.com/) and run the following command.  This command will not affect the original files.

```bash
cd /var/www/training.thediygolfer.com/public_html/
# Finds all files in the current directory and deletes them (or symlink if they exist)
find -maxdepth 1 -type l -delete
```

The final thing we need to do is download a production Node process manager to run our Express server on the VPS.

```bash 
# Install pm2
sudo npm install -g pm2

# Run the server 
pm2 start app.js 
```

This will start the application and persist it.  To see more information on managing pm2 processes, see [this cheatsheet](http://pm2.keymetrics.io/docs/usage/quick-start/#cheat-sheet).

### Setting up the Wordpress Site

This section will take you through the setup process of the Wordpress site.  First, we need to install PHPMyAdmin, which is a graphical interface we will host to manage our database.  This GUI will be located at `http://<url-to-virtual-host>/phpmyadmin`.  Similar to how Wordpress gives you the `/wp-admin` URL, this is just an extension to manage the database.  In this example, I will be setting up the development version of my Wordpress site.

Before doing so, we need to create a directory for our site, install Wordpress, and create a symlink to it.

```bash 
mkdir /home/zachgoll/dev.thediygolfer.com

# Create a symlink so /var/www/dev.thediygolfer.com/public_html/ points to 
# Our Wordpress installation in our home directory
sudo ln -s /home/zachgoll/dev.thediygolfer.com/* /var/www/dev.thediygolfer.com/public_html/

# Remove the index.html file we previously created for testing
rm /var/www/dev.thediygolfer.com/public_html/index.html

# Copy Wordpress to your new site
cp -R /var/www/html/* /home/zachgoll/dev.thediygolfer.com/

# Create the wp-config.php file from the sample 
cp /home/zachgoll/dev.thediygolfer.com/wp-config-sample.php /home/zachgoll/dev.thediygolfer.com/wp-config.php

# Create the .htaccess file
touch .htaccess

# You can delete all the comments out of the wp-config.php file now
```

At this point, when you visit `http://dev.thediygolfer.tk` (remember we setup the test domain for this exercise), you will be prompted to setup your Wordpress site.  We still need to configure a few more things.

```bash 
# When prompted for default configs, say NO
sudo apt-get install phpmyadmin

# Add the path to phpmyadmin app to our virtual host file 
# This will "include" the default host configuration for the 
# phpmyadmin application 
echo "Include /etc/phpmyadmin/apache.conf" >> /etc/apache2/sites-available/dev.thediygolfer.com.conf

# Reload the Apache2 server 
sudo systemctl restart apache2
```

If you navigate to `http://dev.thediygolfer.tk/phpmyadmin`, you should see a login screen to your database.  Run the following command to print your password to the terminal.

```bash 
sudo cat /root/.digitalocean_password
```

Your username is "root" and your password is "root_mysql_pass" indicated in the lines you just printed to the terminal.  If this login does not work, follow these troubleshooting steps.

If it does work, click "User Accounts", and then click "Add user account".  Enter a username (ex: wp_dev_thediygolfer) and then a password.  For the Host name, enter "localhost".  Finally make sure to check the box that says "Create database with same name and grant all privileges".

Once the User and database have been created, go back to your terminal and edit the `wp-config.php` file, adding the database name, username, and password.  At the bottom of the file, create a unique prefix for the database tables (or use the existing one if you are importing a database).  Finally, navigate to `https://api.wordpress.org/secret-key/1.1/salt/`, copy the configuration, and replace the configuration in `wp-config.php` (this link generates random codes every time you refresh).

Now, when you return to `http://dev.thediygolfer.tk`, you will see the famous Wordpress 5 Minute Install screen.  If you still see the database screen, you know that your database is not setup correctly.

### Setting Permissions 

If you try to download a Plugin with the current permission settings, it will not work.  We need to give our Apache2 server the correct permissions to be able to write files and folders.  Before doing this, we need to edit one more thing in `wp-config.php`.  Add the following line after the other define() statements.

```php
define('FS_METHOD', 'direct');
```

By adding this definition, we are telling Wordpress to write files and folders directly to the server.  With some Hosts where the administrator has less control, the only way to download and upload plugins in via FTP.  Since we have more control over our server, we can write directly.  After defining this, we still cannot properly add plugins to our site because the Apache2 server does not have permissions.  To fix this, we need to run the following permissioning commands.

```bash 
sudo chown -R www-data:www-data /var/www/dev.thediygolfer.com/public_html/
sudo find /var/www/dev.thediygolfer.com/public_html/ -type d -exec chmod 750 {} \;
sudo find /var/www/dev.thediygolfer.com/public_html/ -type f -exec chmod 640 {} \;
```

These commands will allow the `www-data` user (i.e. the Apache2 server) to create folders (plugins) and modify server files.  It also sets permissions as to what type of files the Apache2 server can modify, create, delete, etc.  One really important thing to remember--you should _not_ add these permissions to the `/home/zachgoll/dev.thediygolfer/` directory.  If you do, you will not be able to edit your site from within the Digital Ocean droplet.

### Enabling HTTPS 

The Wordpress One Click Droplet comes with a utility called "certbot".  Run this for all of the virtual hosts you have setup.

```bash 
certbot --apache -d thediygolfer.com -d dev.thediygolfer.com -d training.thediygolfer.com
```

This command will create a new `.conf` file in the `/etc/apache2/sites-available/` directory.

### Troubleshooting Root Login Issues 

The mysql process can be quite finnicky and cause lots of issues due to the fact that they are multiple versions of sql, multiple ways to start and stop the process, etc.  I have linked to a few helpful Stack Overflow pages below, but here is the general process to changing your root password.

1. Stop the mysql process

```bash 
sudo systemctl stop mysql 

# If this doesn't work, try the following 
# ----------------------------------------
# Get the process id to kill 
sudo cat /var/run/mysqld/mysqld.pid

sudo kill <process-id>
```

2. Start database 

```bash 
sudo mysqld_safe --skip-grant-tables --skip-networking &

mysql -u root 
```

If you get an error here because the directory does not exist, go through the following sequence.

```bash 
sudo mkdir /var/run/mysqld 
sudo chown mysql: /var/run/mysqld 
sudo mysqld_safe --skip-grant-tables --skip-networking &
mysql -u root 
```

3. Edit the User 

At this point, you should be in the sql shell.

```bash 
>sql FLUSH PRIVILEGES;
>sql UPDATE mysql.user SET authentication_string = PASSWORD('new_password')     WHERE User = 'root' AND Host = 'localhost';
>sql quit;
```

Your password should now be reset.

4. Kill the sql process 

```bash 
sudo cat /var/run/mysqld/mysqld.pid

sudo kill <process-id>
```

5. Restart the sql process and apache2 server

```bash 
sudo systemctl start mysql
sudo systemctl restart apache2
```

6. Navigate to your PHPMyAdmin dashboard in the browser and login with the username 'root' and the password you just set!

[Help 1](https://stackoverflow.com/questions/21944936/error-1045-28000-access-denied-for-user-rootlocalhost-using-password-y/42967789#42967789)
[Killing Linux Processes](https://www.tecmint.com/how-to-kill-a-process-in-linux/)
[Help 2](https://stackoverflow.com/questions/37879448/mysql-fails-on-mysql-error-1524-hy000-plugin-auth-socket-is-not-loaded)

### Transferring Databases from One Host to Another 

If you are like me and need to transfer your database, follow the steps below.

1. Navigate to your _old_ hosting site's phpmyadmin interface.
2. Find the database that you want to export 
    * The name of the database is stored in each site's `wp-config.php` file.
3. Navigate to the "export" tab 
4. Use the "Quick" export method
5. Go to your _new_ site's phpmyadmin 
6. Click the "SQL" Tab and run the command `CREATE DATABASE dev_thediygolfer_wordpress` which will create a new database
7. On the left-hand pane, click on the new database 
8. Click the "Import" tab, upload your recently exported sql file, and click "Go" with the default options.

You should now have your old database in place!

### Uploading Plugins from Local Computer to Digital Ocean Droplet

You can use the `scp` utility to transfer files and folders from remote->local or local->remote hosts via ssh.

To upload a Zip file to the Digital ocean droplet, type the command in your _local_ terminal.

```bash 
scp -r -P 22 /path/to/zip/file/on/local/computer/file.zip username@<digital-ocean-droplet-ip-address>:/home/<username>/

# Example:
# scp -r -P 22 ~/all-in-one-wp-migration-unlimited-extension.zip zachgoll@<digital-ocean-droplet-ip-address>:/home/zachgoll/
```

I want to add this plugin, so I am going to run the following command to put it in my site.

```bash
# Download utility if it is not there already
sudo apt install unzip

# Unzip and place in desired directory
sudo unzip all-in-one-wp-migration-unlimited-extension.zip -d /home/zachgoll/dev.thediygolfer.com/wp-content/plugins/
To download a file from your server, just flip the paths.







