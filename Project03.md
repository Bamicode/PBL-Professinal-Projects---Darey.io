# MERN STACK IMPLEMENTATION

In this project, I will implement a web solution based on MERN stack in AWS Cloud.

MERN Web stack consists of following components:

MongoDB: A document-based, No-SQL database used to store application data in a form of documents.

ExpressJS: A server side Web Application framework for Node.js.

ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.

Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

### Prerequisite

Launch my EC2 virtual server with Ubuntu Server OS

My task is to deploy a simple To-Do application that creates To-Do lists.

### Step 1 – Backend configuration

Update ubuntu

    sudo apt update
Upgrade ubuntu

    sudo apt upgrade
Lets get the location of Node.js software from Ubuntu repositories.

    curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
Install Node.js on the server
Install Node.js with the command below

    sudo apt-get install -y nodejs

Note: The command above installs both nodejs and npm. NPM is a package manager for Node like apt for Ubuntu, it is used to install Node modules & packages and to manage dependency conflicts.

Verify the node installation with the command below

    node -v 
Verify the node installation with the command below

    npm -v 
Application Code Setup
Now we create a new directory for your To-Do project:

    mkdir Todo
Run the command below to verify that the Todo directory is created with ls command:

    ls

Now change your current directory to the newly created one:

    cd Todo

Next, use the command npm init to initialise your project, so that a new file named package.json will be created. 
Note: This file will normally contain information about your application and the dependencies that it needs to run.
- Follow the prompts after running the command.
- Press Enter several times to accept default values, 
- Then accept to write out the package.json file by typing yes.

      npm init

![Todo list](https://user-images.githubusercontent.com/96090546/166468319-a6186e23-08df-4fec-84e1-8c2eb0fd645b.PNG)

## INSTALLING EXPRESSJS

To use express, install it using npm:

    npm install express
Now create a file index.js with the command below

    touch index.js
Run ls to confirm that your index.js file is successfully created

    ls
Install the dotenv module

    npm install dotenv
Open the index.js file with the command below

    vim index.js
Type the code below into it and save. Do not get overwhelmed by the code you see. For now, simply paste the code into the file.

    const express = require('express');
    require('dotenv').config();

    const app = express();

    const port = process.env.PORT || 5000;

    app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "\*");
    res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
    next();
    });

    app.use((req, res, next) => {
    res.send('Welcome to Express');
    });

    app.listen(port, () => {
    console.log(`Server running on port ${port}`)
    });

Notice that we have specified to use port 5000 in the code. This will be required later when we go on the browser.

Save and quit.

Now start the server to see if it works. 
Open terminal in the same directory as your index.js file and type:

    node index.js
![running](https://user-images.githubusercontent.com/96090546/166469670-58e2ec0d-89a1-47e6-a2da-494872358be6.PNG)

Now we need to open this port in EC2 Security Groups
![Sec groups](https://user-images.githubusercontent.com/96090546/166469886-667d54aa-554c-4594-937e-f55a5593e23a.PNG)

Now let's test the express JS on our browser. Enter
http://<PublicIP-or-PublicDNS>:5000
  
![express js web page](https://user-images.githubusercontent.com/96090546/166470638-1ef31bcb-73bb-46da-ba80-c4ef605e178c.PNG)
  
### Routes
There are three actions that our To-Do application needs to be able to do:

Create a new task
Display list of all tasks
Delete a completed task
Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE.

For each task, we need to create routes that will define various endpoints that the To-do app will depend on. So let us create a folder routes

      mkdir routes
  
Tip: You can open multiple shells in Putty or Linux/Mac to connect to the same EC2

Change directory to routes folder.

      cd routes
Now, create a file api.js with the command below

      touch api.js
Open the file with the command below

      vim api.js
Copy below code in the file. (Do not be overwhelmed with the code)

    const express = require ('express');
    const router = express.Router();
    router.get('/todos', (req, res, next) => {
    });
    router.post('/todos', (req, res, next) => {
    });
    router.delete('/todos/:id', (req, res, next) => {
    })
    module.exports = router;

### Moving forward, lets create Models.
  
Since the app is going to make use of Mongodb which is a NoSQL database, we need to create a model.

A model is at the heart of JavaScript based applications, and it is what makes it interactive.
  
In essence, the Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties

To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.

Change directory back Todo folder with cd .. and install Mongoose

    npm install mongoose
Create a new folder models :

    mkdir models
Change directory into the newly created ‘models’ folder with

    cd models
Inside the models folder, create a file and name it todo.js

    touch todo.js
Tip: All three commands above can be defined in one line to be executed consequently with help of && operator, like this:

     mkdir models && cd models && touch todo.js
Open the file created with vim todo.js then paste the code below in the file:

    const mongoose = require('mongoose');
    const Schema = mongoose.Schema;
    //create schema for todo
    const TodoSchema = new Schema({
    action: {
    type: String,
    required: [true, 'The todo text field is required']
    }
    })
    //create model for todo
    const Todo = mongoose.model('todo', TodoSchema);
    module.exports = Todo;
  
Now we need to update our routes from the file api.js in ‘routes’ directory to make use of the new model.

In Routes directory, open api.js with vim api.js, delete the code inside with :%d command and paste there code below into it then save and exit
  
    const express = require ('express');
    const router = express.Router();
    const Todo = require('../models/todo');
    router.get('/todos', (req, res, next) => {
    //this will return all the data, exposing only the id and action field to the client
    Todo.find({}, 'action')
    .then(data => res.json(data))
    .catch(next)
    });

    router.post('/todos', (req, res, next) => {
    if(req.body.action){
    Todo.create(req.body)
    .then(data => res.json(data))
    .catch(next)

    }else {
    res.json({
    error: "The input field is empty"
    })
    }
    });
    router.delete('/todos/:id', (req, res, next) => {
    Todo.findOneAndDelete({"_id": req.params.id})
    .then(data => res.json(data))
    .catch(next)
    })

    module.exports = router;
  
## MONGODB DATABASE

### MongoDB Database
We need a database where we will store our data. For this we will make use of mLab. mLab provides MongoDB database as a service solution (DBaaS), so to make life easy, you will need to sign up for a shared clusters free account, which is ideal for our use case.
  
SIgn up on mongodb.com, complete the process, select AWS as the cloud provider, and choose a region near you.

In the index.js file, we specified process.env to access environment variables, but we have not yet created this file. So we need to do that now.

Create a file in your Todo directory and name it .env.

    touch .env
    vi .env
Add the connection string to access the database in it, just as below:

    DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
  
Ensure to update <username>, <password>, <network-address> and <database> according to your setup

Complete a get started checklist as shown on the image below
![mongodb](https://user-images.githubusercontent.com/96090546/166476032-13950ec3-6059-45ae-a077-97cd24d5cfff.PNG)
  
Now we need to update the index.js to reflect the use of .env so that Node.js can connect to the database.

Simply delete existing content in the file, and update it with the entire code below.

To do that using vim editior, follow below steps:

Open the file with vim index.js
  
    vim index.js

Now, paste the entire code below in the file:

    const express = require('express');
    const bodyParser = require('body-parser');
    const mongoose = require('mongoose');
    const routes = require('./routes/api');
    const path = require('path');
    require('dotenv').config();

    const app = express();

    const port = process.env.PORT || 5000;

    //connect to the database
    mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
    .then(() => console.log(`Database connected successfully`))
    .catch(err => console.log(err));

    //since mongoose promise is depreciated, we overide it with node's promise
    mongoose.Promise = global.Promise;

    app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "\*");
    res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
    next();
    });

    app.use(bodyParser.json());

    app.use('/api', routes);

    app.use((err, req, res, next) => {
    console.log(err);
    next();
    });

    app.listen(port, () => {
    console.log(`Server running on port ${port}`)
    });
  
Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the index.js application file.

Start your server using the command:

    node index.js
  
![ruun](https://user-images.githubusercontent.com/96090546/166478883-7d702d43-40db-4d81-823f-c747988261c3.PNG)

### - Testing Backend Code without Frontend using RESTful API
So far we have written backend part of our To-Do application, and configured a database, but we do not have a frontend UI yet. We need ReactJS code to achieve that. But during development, we will need a way to test our code using RESTfulL API. Therefore, we will need to make use of some API development client to test our code.

In this project (03), we will use Postman to test our API. Downloaded and installed Postman

Now open your Postman, create a POST request to the API http://<PublicIP-or-PublicDNS>:5000/api/todos. This request sends a new task to our To-Do list so the application could store it in the database.

Note: make sure your set header key Content-Type as application/json

Adding a new task to the list – HTTP POST request, I got this error on postman;
  
![error on postman](https://user-images.githubusercontent.com/96090546/166481131-f4688fed-9dfe-489e-9270-fb365fb2c823.PNG)
 
![POST](https://user-images.githubusercontent.com/96090546/166517809-0efd606e-9ef3-43e9-9c53-282664c37fba.PNG)
 
After troubleshooting, I was able to figure out the issue from a line of code. 
  
![POST](https://user-images.githubusercontent.com/96090546/166517747-f7b61006-f53f-40dc-bc7f-dc6be459bc70.PNG)
 
Now make a GET request – HTTP GET request
![GET](https://user-images.githubusercontent.com/96090546/166519329-c6ca4589-64dd-4869-9b95-1c8530bdff1a.PNG)
  
  
### Step 2 – Frontend creation

Since we are done with the functionality we want from our backend and API, it is time to create a user interface for a Web client (browser) to interact with the application via API. To start out with the frontend of the To-do app, we will use the create-react-app command to scaffold our app.

In the same root directory as your backend code, which is the Todo directory, run:

    npx create-react-app client
  
This will create a new folder in your Todo directory called client, where you will add all the react code.
  
** At this point, I encountered an error while creating my frontend, I had to first upgrade the version of my node.js
The following commands were used:
  
Update the package repository with the command:

    sudo apt update

Install NVM using the curl command:

    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash

Close and reopen the terminal for system to recognize the changes or run the command:
    
    source ~/.bashrc

Then, verify if you have successfully installed NVM:
    
    nvm --version

Before upgrading Node.js, check which version you have running on the system:
    
    nmw ls

Now you can check for newly available releases with:
    
nvm ls-remote

To install the latest version, use the nvm command with the specific Node.js version:
    
    nvm install [18.1.0]
  
### - Running a React App
  
Before testing the react app, there are some dependencies that need to be installed.

Install concurrently. It is used to run more than one command simultaneously from the same terminal window.

    npm install concurrently --save-dev

Install nodemon. It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.

    npm install nodemon --save-dev

In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.

    "scripts": {
    "start": "node index.js"
    "start-watch": "nodemon index.js",
    "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
    },
  
Proxy configuration in package.json shown below:
![packagejson](https://user-images.githubusercontent.com/96090546/166632174-e885350c-5f20-4af4-bc83-cb5bcc6a54d0.PNG)
  
Now, change directory to ‘client’
  
    cd client
  
Open the package.json file
  
    vi package.json
  
Add the key value pair in the package.json file "proxy": "http://localhost:5000".
  
The whole purpose of adding the proxy configuration in number 3 above is to make it possible to access the application directly from the browser by simply calling the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos

Now, ensure you are inside the Todo directory, and simply do:

    npm run dev
  
Your app should open and start running on <Public address>:3000 as shown below.
  
![React page](https://user-images.githubusercontent.com/96090546/166632257-3c1aca8c-c5d8-412e-bbc5-4b84da9d4a24.PNG)
  
### Creating React Components
  
One of the advantages of react is that it makes use of components, which are reusable and also makes code modular. For our Todo app, there will be two stateful components and one stateless component.
  
From your Todo directory run

    cd client
move to the src directory

    cd src
Inside your src folder create another folder called components

    mkdir components
Move into the components directory with

    cd components
Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js.

    touch Input.js ListTodo.js Todo.js
Open Input.js file

    vi Input.js
  
Copy and paste the following:

    import React, { Component } from 'react';
    import axios from 'axios';

    class Input extends Component {

    state = {
    action: ""
    }

    addTodo = () => {
    const task = {action: this.state.action}

        if(task.action && task.action.length > 0){
          axios.post('/api/todos', task)
            .then(res => {
              if(res.data){
                this.props.getTodos();
                this.setState({action: ""})
              }
            })
            .catch(err => console.log(err))
        }else {
          console.log('input field required')
        }

    }

    handleChange = (e) => {
    this.setState({
    action: e.target.value
    })
    }

    render() {
    let { action } = this.state;
    return (
    <div>
    <input type="text" onChange={this.handleChange} value={action} />
    <button onClick={this.addTodo}>add todo</button>
    </div>
    )
    }
    }

    export default Input

Now, to make use of Axios, which is a Promise based HTTP client for the browser and node.js, there's need to cd into the client from the terminal and run yarn add axios or npm install axios.

Move to the src folder

    cd ..
Move to clients folder

    cd ..
Install Axios

    npm install axios
  
Go to ‘components’ directory using 

    cd src/components
After that open your ListTodo.js usinng vim editor

    vi ListTodo.js
  
Now in the ListTodo.js copy and paste the following code;

    import React from 'react';

    const ListTodo = ({ todos, deleteTodo }) => {

    return (
    <ul>
    {
    todos &&
    todos.length > 0 ?
    (
    todos.map(todo => {
    return (
    <li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
    )
    })
    )
    :
    (
    <li>No todo(s) left</li>
    )
    }
    </ul>
    )
    }

    export default ListTodo
  
Then in Todo.js file you write the following code
  
    import React, {Component} from 'react';
    import axios from 'axios';

    import Input from './Input';
    import ListTodo from './ListTodo';

    class Todo extends Component {

    state = {
    todos: []
    }

    componentDidMount(){
    this.getTodos();
    }

    getTodos = () => {
    axios.get('/api/todos')
    .then(res => {
    if(res.data){
    this.setState({
    todos: res.data
    })
    }
    })
    .catch(err => console.log(err))
    }

    deleteTodo = (id) => {

        axios.delete(`/api/todos/${id}`)
          .then(res => {
            if(res.data){
              this.getTodos()
            }
          })
          .catch(err => console.log(err))

    }

    render() {
    let { todos } = this.state;

        return(
          <div>
            <h1>My Todo(s)</h1>
            <Input getTodos={this.getTodos}/>
            <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
          </div>
        )

    }
    }

    export default Todo;
  
We need to make little adjustment to our react code. Delete the logo and adjust our App.js to look like this.

Move to the src folder

    cd ..
Make sure that you are in the src folder and run

    vi App.js
  
Copy and paste the code below into it;
  
    import React from 'react';

    import Todo from './components/Todo';
    import './App.css';

    const App = () => {
    return (
    <div className="App">
    <Todo />
    </div>
    );
    }

    export default App;
  
After pasting, exit the editor.

In the src directory open the App.css

    vi App.css
  
Then paste the following code into App.css:

    .App {
    text-align: center;
    font-size: calc(10px + 2vmin);
    width: 60%;
    margin-left: auto;
    margin-right: auto;
    }

    input {
    height: 40px;
    width: 50%;
    border: none;
    border-bottom: 2px #101113 solid;
    background: none;
    font-size: 1.5rem;
    color: #787a80;
    }

    input:focus {
    outline: none;
    }

    button {
    width: 25%;
    height: 45px;
    border: none;
    margin-left: 10px;
    font-size: 25px;
    background: #101113;
    border-radius: 5px;
    color: #787a80;
    cursor: pointer;
    }

    button:focus {
    outline: none;
    }

    ul {
    list-style: none;
    text-align: left;
    padding: 15px;
    background: #171a1f;
    border-radius: 5px;
    }

    li {
    padding: 15px;
    font-size: 1.5rem;
    margin-bottom: 15px;
    background: #282c34;
    border-radius: 5px;
    overflow-wrap: break-word;
    cursor: pointer;
    }

    @media only screen and (min-width: 300px) {
    .App {
    width: 80%;
    }

    input {
    width: 100%
    }

    button {
    width: 100%;
    margin-top: 15px;
    margin-left: 0;
    }
    }

    @media only screen and (min-width: 640px) {
    .App {
    width: 60%;
    }

    input {
    width: 50%;
    }

    button {
    width: 30%;
    margin-left: 10px;
    margin-top: 0;
    }
    }
  
Now save and exit.
  
In the src directory open the index.css

    vim index.css
  
Copy and paste the code below:

    body {
    margin: 0;
    padding: 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
    "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
    sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    box-sizing: border-box;
    background-color: #282c34;
    color: #787a80;
    }

    code {
    font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
    monospace;
    }
  
Go back to the Todo directory,

    cd ../..
  
When you are in the Todo directory run:

    npm run dev
  
Viola!!! Here is my TOdo List:
  
![My Todo](https://user-images.githubusercontent.com/96090546/166642011-c0dbe9d2-7638-48d1-b682-75da0e7f3f21.PNG)
  
![My Todo list 2](https://user-images.githubusercontent.com/96090546/166642015-9b45c843-c736-4588-bcfd-d62f09bb3e0b.PNG)
  
 DONE!!!
