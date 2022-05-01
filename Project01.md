# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. The example we'll talk about in this project is LAMP.

LAMP (Linux, Apache, MySQL, PHP or Python, or Perl)

## LAMP stack Meaning

LAMP stack is an open source technology that allows web developers to build, deploy, and manage web applications. Common example of open source web applications are WordPress and Drupal.

## - Components
LAMP stands for Linux, Apache, MySQL, PHP or Python, or Perl. Together, they provide a proven set of software for delivering high-performance web applications. Each component contributes essential capabilities to the stack:

Linux: The operating system. Linux is a free and open source operating system (OS) that has been around since the mid-1990s. Today, it has an extensive worldwide user base that extends across industries. Linux is popular in part because it offers more flexibility and configuration options than some other operating systems.

Apache: The web server. The Apache web server processes requests and serves up web assets via HTTP so that the application is accessible to anyone in the public domain over a simple web URL. Developed and maintained by an open community, Apache is a mature, feature-rich server that runs a large share of the websites currently on the internet. 

MySQL: The database. MySQL is an open source relational database management system for storing application data. With My SQL, you can store all your information in a format that is easily queried with the SQL language. SQL is a great choice if you are dealing with a business domain that is well structured, and you want to translate that structure into the backend. MySQL is suitable for running even large and complex sites. See "SQL vs. NoSQL Databases: What's the Difference?" for more information on SQL and NoSQL databases.

PHP: The programming language. The PHP open source scripting language works with Apache to help you create dynamic web pages. You cannot use HTML to perform dynamic processes such as pulling data out of a database. To provide this type of functionality, you simply drop PHP code into the parts of a page that you want to be dynamic. 

## How the components work together

A high-level look at the LAMP stack order of execution shows how the elements interoperate. The process starts when the Apache web server receives requests for web pages from a user’s browser. If the request is for a PHP file, Apache passes the request to PHP, which loads the file and executes the code contained in the file. PHP also communicates with MySQL to fetch any data referenced in the code. 

PHP then uses the code in the file and the data from the database to create the HTML that browsers require to display web pages. The LAMP stack is efficient at handling not only static web pages, but also dynamic pages where the content may change each time it is loaded depending on the date, time, user identity and other factors. 

After running the file code, PHP then passes the resulting data back to the Apache web server to send to the browser. It can also store this new data in MySQL. And of course, all of these operations are enabled by the Linux operating system running at the base of the stack.

## Step 0 – Prerequisites - Connecting to my EC2 instance on my AWS account, using Putty on Windows OS.

Below is my AWS EC2 instance of t2.micro family 
![EC2 Instance LAMP](https://user-images.githubusercontent.com/96090546/166124659-3691d31c-f0d4-401b-be2e-731df287b157.JPG)

Pictorial view of connecting to my EC2 instance using Putty Ubuntu Server 22.04 LTS (HVM)
![Putty 1](https://user-images.githubusercontent.com/96090546/166124662-4892ff6c-3c1c-4f47-b7bb-69ea66658e79.JPG)
![Putty 2](https://user-images.githubusercontent.com/96090546/166124665-cb9a3978-8d67-4ff1-ac50-4e8ddaa776b8.JPG)

Pictorial view of connecting to my EC2 instance using Ubuntu Server 22.04 LTS (HVM)
![EC2 Instance on Ubuntu](https://user-images.githubusercontent.com/96090546/166127187-f961b5e0-8783-4ced-a6ef-9dce0c77af7d.JPG)

Pictorial view of connecting to my EC2 instance using MobaXterm
![EC2 Instance_MobaXterm](https://user-images.githubusercontent.com/96090546/166127334-beb90f8d-1684-4960-af6e-44c8f8113920.JPG)


## STEP 1 — INSTALLING APACHE AND UPDATING THE FIREWALL

To install Apache using Ubuntu’s package manager ‘apt’:

#update a list of packages in package manager
sudo apt update

#run apache2 package installation
sudo apt install apache2

To verify that apache2 is running as a Service in the OS, use following command;
sudo systemctl status apache2

Accessing it locally in MabaXterm terminal by running:

 curl http://localhost:80
or
 curl http://127.0.0.1:80
 
![Apache2 Web server](https://user-images.githubusercontent.com/96090546/166124927-bb479d36-76f6-4478-94c3-2e36d1f9e6bd.JPG)

Here is a pictorial interpretation of a successful connection to a Linux server in the Cloud
![Connecting to AWS Cloud - Ubuntu server](https://user-images.githubusercontent.com/96090546/166121155-95d5b286-339a-4c0f-8a69-965507206162.JPG)

# STEP 2 — INSTALLING MYSQL
Run the following command
sudo apt install mysql-server

![Installing MySQL](https://user-images.githubusercontent.com/96090546/166125163-36d63e22-36d7-4f34-840e-67143be145be.JPG)

# STEP 3 — INSTALLING PHP
Run the following command
sudo apt install php libapache2-mod-php php-mysql

The PHP version as at this time using the below command:
php -v
![php version](https://user-images.githubusercontent.com/96090546/166125263-ed525468-fa29-436b-ba27-408769ff642c.JPG)

To test the setup with a PHP script, it’s best to set up a proper Apache Virtual Host to hold the website’s files and folders. Virtual host allows us to have multiple websites located on a single machine.
![Virtual host sampl](https://user-images.githubusercontent.com/96090546/166125354-f0ef6c06-a874-492a-ab9d-f611988ee36f.JPG)

# STEP 4 — CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE

Create the directory for projectlamp using ‘mkdir’ command as follows:
 sudo mkdir /var/www/projectlamp

Next, assign ownership of the directory with your current system user:
 sudo chown -R $USER:$USER /var/www/projectlamp

Then, create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor. Here, we’ll be using vi or vim (They are the same by the way):

sudo vi /etc/apache2/sites-available/projectlamp.conf
This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

Using the command line sudo ls /etc/apache2/sites-available,
![Virtual Host](https://user-images.githubusercontent.com/96090546/166125955-83a3f558-a584-452f-9d17-58b89de1a9f3.JPG)

With this VirtualHost configuration, we’re telling Apache to serve projectlamp using /var/www/projectlampl as its web root directory.

Use a2ensite command to enable the new virtual host:
sudo a2ensite projectlamp

To disable the default website that comes installed with Apache. This is required if we’re not using a custom domain name, because in this case Apache’s default configuration would overwrite your virtual host. To disable Apache’s default website,
use a2dissite command , type:
 sudo a2dissite 000-default

To make sure the configuration file doesn’t contain syntax errors, run:

sudo apache2ctl configtest
Finally, reload Apache so these changes take effect:
 sudo systemctl reload apache2

The new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:

sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-  data/public-ipv4) > /var/www/projectlamp/index.html

Now on my browser, the below is the result. This shows that the Apache virtual host is working as expected.
In the output we can see server’s public hostname (DNS name) and public IP address.
![echo LAMP -Index html created on virtual host](https://user-images.githubusercontent.com/96090546/166126002-5240f78e-e37b-44d0-b51a-86daa2ac584c.JPG)

## STEP 5 — ENABLE PHP ON THE WEBSITE

With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root, bringing back the regular application page.

To change this behavior, there's need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:

sudo vim /etc/apache2/mods-enabled/dir.conf
<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>

After saving and closing the file, reload Apache so the changes take effect using:
sudo systemctl reload apache2

Finally, creqte a PHP script to test that PHP is correctly installed and configured on your server.

Now that there's a custom location to host the website’s files and folders, create a PHP test script to confirm that Apache is able to handle and process requests for PHP files.

Then create a new file named index.php inside the custom web root folder:
vim /var/www/projectlamp/index.php

This will open a blank file. Adding the following text, which is valid PHP code, inside the file:
<?php
phpinfo();

Refresh the browser to confirm that php was installed correctly on my web server,
![ph-p on my web server](https://user-images.githubusercontent.com/96090546/166127466-853e192f-e58a-4117-bacb-29978e224d78.JPG)

References:
- Darey.io DevOps Engineer Scholarship and 
- https://www.ibm.com/cloud/learn/lamp-stack-explained
