# MEAN STACK DEPLOYMENT TO UBUNTU IN AWS

MEAN Stack is a combination of following components:

MongoDB (Document database) – Stores and allows to retrieve data.
Express (Back-end application framework) – Makes requests to Database for Reads and Writes.
Angular (Front-end application framework) – Handles Client and Server Requests
Node.js (JavaScript runtime environment) – Accepts requests and displays results to end user

## Task
In this assignment you are going to implement a simple Book Register web form using MEAN stack.

## Step 1: Install NodeJs
Node.js is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js is used in this tutorial to set up the Express routes and AngularJS controllers.

## Step 3: Install Express and set up routes to the server
Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database.

We also will use Mongoose package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register.

sudo npm install express mongoose
In ‘Books’ folder, create a folder named apps

mkdir apps && cd apps
Create a file named routes.js

vi routes.js

Copy and paste the code below into routes.js

![apps file](https://user-images.githubusercontent.com/96090546/166719569-a40fdbdf-def8-4a95-8b58-901c02f306ad.PNG)

In the ‘apps’ folder, create a folder named models

mkdir models && cd models
Create a file named book.js

vi book.js
Copy and paste the code below into ‘book.js’

![,,,](https://user-images.githubusercontent.com/96090546/166719786-42b08785-3b4e-4cdf-b939-bee150e227b1.PNG)

Step 4 – Access the routes with AngularJS
AngularJS provides a web framework for creating dynamic views in your web applications. In this tutorial, we use AngularJS to connect our web page with Express and perform actions on our book register.

Change the directory back to ‘Books’

cd ../..
Create a folder named public

mkdir public && cd public
Add a file named script.js

vi script.js

Copy and paste the Code below (controller configuration defined) into the script.js file.

![7](https://user-images.githubusercontent.com/96090546/166720484-1c24194a-f08a-43d9-bf0d-9fc1831ed965.PNG)

In public folder, create a file named index.html;

vi index.html
Cpoy and paste the code below into index.html file.

![8](https://user-images.githubusercontent.com/96090546/166722589-b82a1dd4-6821-4397-8ddf-eed712f57315.PNG)

![9](https://user-images.githubusercontent.com/96090546/166722596-5a866251-035a-4387-9a49-bf02772cbbc7.PNG)

Change the directory back up to Books

cd ..
Start the server by running this command:

node server.js
The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what curl command returns locally.

curl -s http://localhost:3300

It shall return an HTML page, it is hardly readable in the CLI, but we can also try and access it from the Internet.

Using MobaXterm, see below

![mobaxterm](https://user-images.githubusercontent.com/96090546/166723325-f9153694-c302-4dc8-bd12-c7ef0c7699df.PNG)


For this – we open TCP port 3300 in your AWS Web Console for your EC2 Instance. Done

Now you can access our Book Register web application from the Internet with a browser using Public IP address or Public DNS name.

Quick reminder how to get your server’s Public IP and public DNS name:

You can find it in your AWS web console in EC2 details
Run curl -s http://169.254.169.254/latest/meta-data/public-ipv4 for Public IP address or 

curl -s http://169.254.169.254/latest/meta-data/public-hostname for Public DNS name.

This is how your Web Book Register Application will look like in browser:

![Books app](https://user-images.githubusercontent.com/96090546/166724931-d465db93-b641-4c19-841c-22018f3cbfde.PNG)



