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



