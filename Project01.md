# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. The example we'll talk about in this project is LAMP.

LAMP (Linux, Apache, MySQL, PHP or Python, or Perl)

## Self Study

## LAMP stack Meaning

LAMP stack is an open source technology that allows web developers to build, deploy, and manage web applications. Common example of open source web applications are WordPress and Drupal.

## - Components
LAMP stands for Linux, Apache, MySQL, PHP or Python, or Perl. Together, they provide a proven set of software for delivering high-performance web applications. Each component contributes essential capabilities to the stack:

Linux: The operating system. Linux is a free and open source operating system (OS) that has been around since the mid-1990s. Today, it has an extensive worldwide user base that extends across industries. Linux is popular in part because it offers more flexibility and configuration options than some other operating systems.

Apache: The web server. The Apache web server processes requests and serves up web assets via HTTP so that the application is accessible to anyone in the public domain over a simple web URL. Developed and maintained by an open community, Apache is a mature, feature-rich server that runs a large share of the websites currently on the internet. 

MySQL: The database. MySQL is an open source relational database management system for storing application data. With My SQL, you can store all your information in a format that is easily queried with the SQL language. SQL is a great choice if you are dealing with a business domain that is well structured, and you want to translate that structure into the backend. MySQL is suitable for running even large and complex sites. See "SQL vs. NoSQL Databases: What's the Difference?" for more information on SQL and NoSQL databases.

PHP: The programming language. The PHP open source scripting language works with Apache to help you create dynamic web pages. You cannot use HTML to perform dynamic processes such as pulling data out of a database. To provide this type of functionality, you simply drop PHP code into the parts of a page that you want to be dynamic. 
PHP is designed for efficiency. It makes programming easier—and a bit more fun—by allowing you to write new code, hit refresh, and immediately see the resulting changes without the need for compiling. If you prefer, you can swap out PHP in favor of Perl or the increasingly popular Python language.

## How the components work together

A high-level look at the LAMP stack order of execution shows how the elements interoperate. The process starts when the Apache web server receives requests for web pages from a user’s browser. If the request is for a PHP file, Apache passes the request to PHP, which loads the file and executes the code contained in the file. PHP also communicates with MySQL to fetch any data referenced in the code. 

PHP then uses the code in the file and the data from the database to create the HTML that browsers require to display web pages. The LAMP stack is efficient at handling not only static web pages, but also dynamic pages where the content may change each time it is loaded depending on the date, time, user identity and other factors. 

After running the file code, PHP then passes the resulting data back to the Apache web server to send to the browser. It can also store this new data in MySQL. And of course, all of these operations are enabled by the Linux operating system running at the base of the stack.



## Step 0 – Prerequisites - Connecting to my EC2 instance on my AWS account, using Putty on Windows OS.

Below is my AWS EC2 instance of t2.micro family 
!![EC2 Instance LAMP](https://user-images.githubusercontent.com/96090546/166122396-28ea7a59-e8ce-4fb1-9e38-783c579342f7.JPG)

Pictorial view of connecting to my EC2 instance using Putty Ubuntu Server 22.04 LTS (HVM)
![Putty 1](https://user-images.githubusercontent.com/96090546/166122313-8a71c012-2e8d-4d1e-9174-6b88819676c8.JPG)
![Putty 2](https://user-images.githubusercontent.com/96090546/166122322-bcd32bd3-1ab4-48b2-bc2c-87c571ebe992.JPG)

Pictorial view of connecting to my EC2 instance using Ubuntu Server 22.04 LTS (HVM)


Pictorial view of connecting to my EC2 instance using MobaXterm

## STEP 1 — INSTALLING APACHE AND UPDATING THE FIREWALL


Here is a pictorial interpretation of a successful connection to a Linux server in the Cloud
![Connecting to AWS Cloud - Ubuntu server](https://user-images.githubusercontent.com/96090546/166121155-95d5b286-339a-4c0f-8a69-965507206162.JPG)


