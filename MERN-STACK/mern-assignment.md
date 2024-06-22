# Prepare MERN Pre-requistes

for this project we need to prepare the following prerequisites

  Set up AWS Account.
  Create an Ubuntu EC2 Instance
  Update the Firewall rules

 to kick-start this project, i created an EC2 instance on AWS. i choose the Ubuntu linux distro and set up the appropriate inbound and outbound rules which i connected it using ssh.

 our EC2 instance is up and running for the developing and Deploying of the TO-DO Web application and our Ubuntu EC2 will be the host for the backend and frontend of this application.


 # Configure Backend
to install Node.js, run the following commands:

``` 
   sudo apt update
```
![alt text](<Photos.md/Screenshot (88).png>)
then upgrade ubuntu

```
   sudo apt upgrade
```
![alt text](<Photos.md/Screenshot (89).png>)

add repository for Node.js package for Ubuntu.

```
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```
![alt text](<Photos.md/Screenshot (91).png>)

# Install Nodes.js on the Server

```
    sudo apt-get install -y nodejs
```

![alt text](<Photos.md/Screenshot (93).png>)

gitThe command above installs both nodejs and npm. NPM is a package manager for Node like apt for Ubuntu, it is used to install Node modules & packages and to manage dependency conflicts.

lets check the version of Node.js and npm installed.

```
  node -v
```

```
   npm -v
```
![alt text](<Photos.md/Screenshot (95).png>)

Create a new directory for TO-DO Project:

```
    mkdir Todo
```

verify that the Todo directory is created , run this command

```
ls
```

![alt text](<Photos.md/Screenshot (97).png>)
now change the current directoy to the newly created one:

```
cd Todo
```

with this commands, next we initialize a new Node.js project in the Todo directory using the following command:

```
npm init
```

the npm init creates a package.json file in the Todo directory, This file will contains information about the application and the dependencies that it needs to run the Todo  app. 

![alt text](<Photos.md/Screenshot (137).png>)





## Install ExpressJS

expressJS is a frame work of Node.js. the development team will use Express.js to define the routes of the appication based in HTTP methods and URLs. To install Express.js , run the following command:

```
npm install express
```
![alt text](<Photos.md/Screenshot (103).png>)

create a file index.js 

```
touch index.js
```

Run ls to confirm if index.js file is created Then install the dotenv module

```
npm install dotenv
```

![alt text](<Photos.md/Screenshot (104).png>)

open the index.js file with this command:

```
vim index.js
```

once the index.js file is opened input the codes below:

```
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
```

Use :w to save in vim and use :qa to exit vim.

when a request is being made the server listens on port 5000 by default. 

start the server to see if it works. Open your terminal in the same directory as your index.js file and type:


```
    node index.js
```

![alt text](<Photos.md/Screenshot (108).png>)

if installed you should see Server running on port 5000 in your terminal. we need to ensure that our EC2 instance allows traffic on port 5000 by updating the inbound rules for the EC2 instance, added a custom TCP rule for port 5000 to allow traffic from all IP addresses.

Open up your browser and try to access your server's Public IP or Public DNS name followed by port 5000


![alt text](<Photos.md/Screenshot (109).png>)

notice also that in the index.js snippet, we are using dotenv package to load the port number on which the apps backend listens, from the environment variable. we will need to install the dotenv package. to install the dotenv package , run the folloing command:

```
npm install dotenv
```


# Routes

we need to create routes that will define various endpoints that the To-do app will depend on. So let us create a folder routes

```
mkdir routes
```
Change directory to routes folder.

```
cd routes
```


create a file api.js with the command below

```
touch api.js
```

![alt text](<Photos.md/Screenshot (111).png>)

Open the file with the command below

```
vim api.js
```


```
const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;
```



# Models

The app is going to make use of Mongodb which is a NoSQL database, to create a Schema and a model directory, install mongoose which is a Node.js package that makes working with mongodb easier.

Change directory back Todo folder with cd .. and install Mongoose


```
npm install mongoose
```

![alt text](<Photos.md/Screenshot (123).png>)


Create a new folder with mkdir models command, change the directory into the newly created models folder with cd models, inside the models folder, create a file and name it todo.js.

```
touch todo.js
```

 created a file with vim todo.js then paste the code below in the file:

```
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
```

 update our routes from the file api.js in 'routes' directory to make use of the new model then open api.js with vim api.js, delete the code inside with :%d command and paste there code below into it then save and exit


```
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
```



# Set up MongoDB Database

set up MongoDB on local EC2 instance,
use a Database Management as a serive on the cloud. (DBaas) but for this service we need a database where to store our data, so we will make use of mLab.


# setup mlab service

1.  i created a free account on mlab.
2. i created a new database
3. i created a new user for the database.

i created a database To-Do list and a collection To-Do items, a user db-admin with a password to access it then i retrieved the connection string for the database. this connection string will be used to connect the backend to the mongoDB database.


![alt text](<Photos.md/Screenshot (115).png>)



create a file in yiur To-Do directory and name it .env.

```
   touch .env
vi .env
```

connect the strings to access the database

```
  DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
  ```

  I updated username, password, network-address and database according to my setup

  I updated the index.js to reflect the use of .env so that Node.js can connect to the database.

 I deleted existing content in the file, and update it with the entire code below using vim index.js



```
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
```

start the server using the command;

```
  node index.js
```

![alt text](<Photos.md/Screenshot (130).png>)

If you see 'Database connected successfully' then we have our backend configured


# Testing Backend code without Frontend using RESTful API


in this project i will use postman to test our API

install postman

create a POST requestto the API
 http://<PublicIP-or-PublicDNS>:5000/api/todos. This request sends a new task to our To-Do list so the application could store it in the database. make sure that the set header key Content type as appilication/json

![alt text](<Photos.md/Screenshot (132)-1.png>)


 Create a GET request to your API on http://<PublicIP-or-PublicDNS>:5000/api/todos. This request retrieves all existing records from out To-do application (backend requests these records from the database and sends it us back as a response to GET request).


 by now backend part of our To-Do application have been tested, made sure that it supports all three operations we wanted:
 
 [x] Display a list of tasks - HTTP GET request
 [x] Add a new task to the list - HTTP POST request
 [x] Delete an existing task from the list - HTTP DELETE request
 
# Frontend creation


To start out with the frontend of the To-do app, we will use the create-react-app command to scaffold our app.

In the same root directory as your backend code, which is the Todo directory, run:

```
   $ npx create-react-app client
```

it will create a new folder in To-Do directory called client where we will add all the react code.



 dependencies that need to be installed.

1. Install concurrently. It is used to run more than one command simultaneously from the same terminal window

```
  $ npm install concurrently --save-dev
```

2. Install nodemon. 


```
$ npm install nodemon --save-dev
```
It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.

3. In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.


```
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```

# Configure Proxy in package.json

1. change directory to 'client'

```
cd client
```

2. Open the package.json file

```
vi package.json
```
3. Add the key value pair in the package.json file "proxy": "http://localhost:5000".
4. 
5. Before we test it in the browser let's update the inbound rules of the EC2 instance to allow traffic on port 3000.
we will be running the frontend server on port 3000. I added a custom TCP rule for port 3000 to allow traffic from all IP addresses.



![alt text](<Photos.md/Screenshot (145).png>)


ensure you are inside the Todo directory, run:

```
  npm run dev
```

![alt text](<Photos.md/Screenshot (156).png>)


We should see - on the browser if we load http://3.85.210.229:3000


![alt text](<Photos.md/Screenshot (151).png>)


now the app is open and running on localhost:3000 by opening TCP port 3000 on EC2 and adding a new Security Group rule. 

# Creating React Components

From Todo directory run:

```
cd client
```

move to the src directory

```
   cd src
```
![alt text](<Photos.md/Screenshot (152).png>)


Inside src folder create another folder called components

```
  mkdir components
```

Move into the components directory with

```
 cd components
 ```

 Inside 'components' directory create three files Input.js, ListTodo.js and Todo.js.


```
 touch Input.js ListTodo.js Todo.js
 ```

 open Input.js

 ```
   vi Input.js
 ```
copy and paste 

```
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
```


To make use of Axios, which is a Promise based HTTP client for the browser and node.js, i cd into my client from my terminal and run yarn add axios or npm install axios.

Move to the src folder

```
cd ..
```

Move to clients folder

```
  cd ..
```

Install Axios


``` 
$ npm install axios
```

Go to 'components' directory

```
cd src/components
```
After that open your ListTodo.js

```
  vi ListTodo.js
```

in the ListTodo.js copy and paste the following code


```
 import React from'react';

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
```

then in your Todo.js file write the following code

```
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
```


![alt text](<Photos.md/Screenshot (161).png>)


 we need to make adjusment to our react code. delete the  logo and adjust our App.js to look like this.


Move to the src folder

```
cd ..
```

Make sure that you are in the src folder and run

```
  vi App.js
```

Copy and paste the code below into it



```
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
```

After pasting, exit the editor.

In the src directory open the App.css


```
vi App.css
```
paste the following code into App.css:


```
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
```

Exit

In the src directory open the index.css

```
vim index.css
```

Copy and paste the code below:


```
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
```


![alt text](<Photos.md/Screenshot (158).png>)


Go to the Todo directory

```
cd ../..
```

run Todo directory:


```
npm run dev
```

![alt text](<Photos.md/Screenshot (160).png>)

without errors while saving the files, our To-Do app is ready and functional with creating a task, delecting a task and viewing all the tasks.


# Summary

In this project, we set up a MERN stack application development environment on AWS. We created a TO-DO app using the MERN stack. We set up the backend using Node.js and Express.js. We defined the routes for the TO-DO app and modeled the TO-DO items. We connected the backend to the MongoDB database using mLab. We tested the API endpoints using Postman. We set up the frontend using React. We created React components to display the TO-DO items. We tested the frontend in the browser. The TO-DO app is working as expected. The user can add a new TO-DO item, retrieve all TO-DO items, update a TO-DO item, and delete a TO-DO item. The MERN stack application development environment is now set up on AWS. The backend is working as expected and the frontend is also working as expected.









