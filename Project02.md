# WEB STACK IMPLEMENTATION (LEMP STACK)

### Step 1 – Installing the Nginx Web Server
In order to display web pages to our site visitors, we are going to employ Nginx, a high-performance web server. We’ll use the apt package manager to install this package.

Since this is our first time using apt for this session, start off by updating your server’s package index. Following that, you can use apt install to get Nginx installed:

    sudo apt update
    sudo apt install nginx
When prompted, enter Y to confirm that you want to install Nginx. Once the installation is finished, the Nginx web server will be active and running on your Ubuntu 20.04 server.

To verify that nginx was successfully installed and is running as a service in Ubuntu, run:

    sudo systemctl status nginx

![nginx](https://user-images.githubusercontent.com/96090546/166644386-e0bef2bd-3d6d-46b8-a4f2-e0750bb34d02.PNG)

### Step 2 — Installing MySQL

MySQL is a Database management System.
Run the following codes

    sudo apt install mysql-server

    sudo mysql_secure_installation

![mysql](https://user-images.githubusercontent.com/96090546/166648432-cadcec75-d49a-4a64-b376-831309c65c0e.PNG)

### Step 3 – Installing PHP

Now that Nginx has been installed to serve our content and MySQL installed to store and manage your data. Now install PHP to process code and generate dynamic content for the web server.

While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. You’ll need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

To install these 2 packages at once, run:

    sudo apt install php-fpm php-mysql

When prompted, type Y and press ENTER to confirm installation.

New website is now active, but the web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test that your new server block works as expected:

### Step 4 — Configuring Nginx to Use PHP Processor

When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. In this guide, we will use projectLEMP as an example domain name.

On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. Instead of modifying /var/www/html, we’ll create a directory structure within /var/www for the your_domain website, leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites.

Create the root web directory for your_domain as follows:

    sudo mkdir /var/www/projectLEMP
    
Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:

    sudo chown -R $USER:$USER /var/www/projectLEMP
    
Then, open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use nano:

    sudo nano /etc/nginx/sites-available/projectLEMP
    
This will create a new blank file. Paste in the following bare-bones configuration:

    #/etc/nginx/sites-available/projectLEMP

    server {
        listen 80;
        server_name projectLEMP www.projectLEMP;
        root /var/www/projectLEMP;

        index index.html index.htm index.php;

        location / {
            try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
         }

        location ~ /\.ht {
            deny all;
        }

    }

Here’s what each of these directives and location blocks do:

listen — Defines what port Nginx will listen on. In this case, it will listen on port 80, the default port for HTTP.

root — Defines the document root where the files served by this website are stored.

index — Defines in which order Nginx will prioritize index files for this website. It is a common practice to list index.html files with a higher precedence than index.php files to allow for quickly setting up a maintenance landing page in PHP applications. You can adjust these settings to better suit your application needs.

server_name — Defines which domain names and/or IP addresses this server block should respond for. Point this directive to your server’s domain name or public IP address.

location / — The first location block includes a try_files directive, which checks for the existence of files or directories matching a URI request. If Nginx cannot find the appropriate resource, it will return a 404 error.

location ~ \.php$ — This location block handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.4-fpm.sock file, which declares what socket is associated with php-fpm.

location ~ /\.ht — The last location block deals with .htaccess files, which Nginx does not process. By adding the deny all directive, if any .htaccess files happen to find their way into the document root ,they will not be served to visitors.

When done editing, save and close the file. Using nano editor, type CTRL+X and then y and ENTER to confirm.

Activate the configuration by linking to the config file from Nginx’s sites-enabled directory:

    sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
    
This will tell Nginx to use the configuration next time it is reloaded. You can test your configuration for syntax errors by typing:

    sudo nginx -t
You shall see following message:

nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:

    sudo unlink /etc/nginx/sites-enabled/default
    
When you are ready, reload Nginx to apply the changes:

    sudo systemctl reload nginx
    
Your new website is now active, but the web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test that your new server block works as expected:

    sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html

Now go to your browser and try to open your website URL using IP address:

http://Public-IP-Address:80
  
  ![lemp on browser](https://user-images.githubusercontent.com/96090546/166649728-1270b0d7-065e-4826-b72a-60ba367c7e33.PNG)
  
  
If you see the text from ‘echo’ command you wrote to index.html file, then it means your Nginx site is working as expected.

In the output you will see your server’s public hostname (DNS name) and public IP address. You can also access your website in your browser by public DNS name, not only by IP – try it out, the result must be the same (port is optional)

http://Public-DNS-Name:80

We can leave this file in place as a temporary landing page for your application until you set up an index.php file to replace it. Once you do that, remember to remove or rename the index.html file from your document root, as it would take precedence over an index.php file by default.

My LEMP stack is now fully configured. In the next step, I’ll create a PHP script to test that Nginx is in fact able to handle .php files within your newly configured website.

### Step 5 – Testing PHP with Nginx

Doing this by creating a test PHP file in the document root. Open a new file called info.php within your document root in the nano text editor:

    sudo nano /var/www/projectLEMP/info.php

Type or paste the following lines into the new file. This is valid PHP code that will return information about your server:

    <?php
    phpinfo();

After checking the relevant information about the PHP server through that page, it’s best to remove the file created as it contains sensitive information about our PHP environment and our Ubuntu server. Use rm to remove that file:

    sudo rm /var/www/your_domain/info.php

![php page](https://user-images.githubusercontent.com/96090546/166827304-591dfd61-3076-4241-ab8a-a3b3fce8064f.PNG)

### Step 6 — Retrieving data from MySQL database with PHP

Here, will create a test database (DB) with simple "To do list" and configure access to it, so the Nginx website would be able to query data from the DB and display it.

We will create a database named example_database and a user named example_user, but you can replace these names with different values.

First, connect to the MySQL console using the root account:

    sudo mysql

To create a new database, run the following command from your MySQL console:

    mysql> CREATE DATABASE project2_database;

Now you can create a new user and grant him full privileges on the database just created.

The following command creates a new user named example_user, using mysql_native_password as default authentication method. We’re defining this user’s password as password, but you should replace this value with a secure password of your own choosing.

    mysql>  CREATE USER 'dele_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';

Now we need to give this user permission over the example_database database:

    mysql> GRANT ALL ON project2_database.* TO 'dele_user'@'%';

This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.

Now exit the MySQL shell with:

    mysql> exit
    
Test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:

    mysql -u example_user -p

After logging in to the MySQL console, confirm that you have access to the example_database database:

    mysql> SHOW DATABASES;

This will give you the following output:

![mysql](https://user-images.githubusercontent.com/96090546/166829493-73d7ce2e-8193-41d7-b948-da19741cb82f.PNG)

Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement:

    mysql>  CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT, content VARCHAR(255), PRIMARY KEY(item_id));

Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:

    mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");

To confirm that the data was successfully saved to your table, run:

    mysql>  SELECT * FROM example_database.todo_list;

![my first datab](https://user-images.githubusercontent.com/96090546/166831539-cccd06f2-8552-443a-9db1-d7dca4e67239.PNG)


Now exit the MySQL console:

    mysql> exit

Now creating a PHP script that will connect to MySQL and query for your content. Create a new PHP file in your custom web root directory using your preferred editor. We’ll use vi for that:

    sudo nano /var/www/projectLEMP/todo_list.php
    
The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results in a list. If there is a problem with the database connection, it will throw an exception.

Copy this content into your todo_list.php script:

![php on root](https://user-images.githubusercontent.com/96090546/166832643-057ac71e-e9fc-4968-b009-2c79c95f58f6.PNG)

Save and close the file when you are done editing

We can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:

That is,

http://<Public_domain_or_IP>/todo_list.php

![My TODo list on web](https://user-images.githubusercontent.com/96090546/166832882-930ea41f-56ec-4165-9c4f-be7a0160dc0d.PNG)


THANK YOU!!!


