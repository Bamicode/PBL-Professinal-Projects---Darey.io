# WEB STACK IMPLEMENTATION (LEMP STACK)

## Step 1 – Installing the Nginx Web Server
In order to display web pages to our site visitors, we are going to employ Nginx, a high-performance web server. We’ll use the apt package manager to install this package.

Since this is our first time using apt for this session, start off by updating your server’s package index. Following that, you can use apt install to get Nginx installed:

sudo apt update
sudo apt install nginx
When prompted, enter Y to confirm that you want to install Nginx. Once the installation is finished, the Nginx web server will be active and running on your Ubuntu 20.04 server.

To verify that nginx was successfully installed and is running as a service in Ubuntu, run:

sudo systemctl status nginx

![nginx](https://user-images.githubusercontent.com/96090546/166644386-e0bef2bd-3d6d-46b8-a4f2-e0750bb34d02.PNG)

## Step 2 — Installing MySQL

MySQL is a Database management System.
Run the following codes

sudo apt install mysql-server

sudo mysql_secure_installation

![mysql](https://user-images.githubusercontent.com/96090546/166648432-cadcec75-d49a-4a64-b376-831309c65c0e.PNG)

New website is now active, but the web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test that your new server block works as expected:

sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html

![lemp on browser](https://user-images.githubusercontent.com/96090546/166649728-1270b0d7-065e-4826-b72a-60ba367c7e33.PNG)

## Step 5 – Testing PHP with Nginx

Doing this by creating a test PHP file in your document root. Open a new file called info.php within your document root in your text editor:

sudo nano /var/www/projectLEMP/info.php

Type or paste the following lines into the new file. This is valid PHP code that will return information about your server:

![php](https://user-images.githubusercontent.com/96090546/166827404-df26952e-c1a9-4e63-ba37-f8bf5c9b31d1.PNG)


![php page](https://user-images.githubusercontent.com/96090546/166827304-591dfd61-3076-4241-ab8a-a3b3fce8064f.PNG)

## Step 6 — Retrieving data from MySQL database with PHP

Here, will create a test database (DB) with simple "To do list" and configure access to it, so the Nginx website would be able to query data from the DB and display it.

We will create a database named example_database and a user named example_user, but you can replace these names with different values.

First, connect to the MySQL console using the root account:

sudo mysql

To create a new database, run the following command from your MySQL console:

mysql> CREATE DATABASE project2_database;

Now you can create a new user and grant him full privileges on the database you have just created.

The following command creates a new user named example_user, using mysql_native_password as default authentication method. We’re defining this user’s password as password, but you should replace this value with a secure password of your own choosing.

mysql>  CREATE USER 'dele_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';

Now we need to give this user permission over the example_database database:

mysql> GRANT ALL ON project2_database.* TO 'dele_user'@'%';

This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.

Now exit the MySQL shell with:

mysql> exit
You can test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:

mysql -u example_user -p

Notice the -p flag in this command, which will prompt you for the password used when creating the example_user user. After logging in to the MySQL console, confirm that you have access to the example_database database:

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

nano /var/www/projectLEMP/todo_list.php
The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results in a list. If there is a problem with the database connection, it will throw an exception.

Copy this content into your todo_list.php script:

![php on root](https://user-images.githubusercontent.com/96090546/166832643-057ac71e-e9fc-4968-b009-2c79c95f58f6.PNG)

Save and close the file when you are done editing

We can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:

That is,

http://<Public_domain_or_IP>/todo_list.php

![My TODo list on web](https://user-images.githubusercontent.com/96090546/166832882-930ea41f-56ec-4165-9c4f-be7a0160dc0d.PNG)

Done!


