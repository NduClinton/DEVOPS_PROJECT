# SIMPLE TO-DO APPLICATION ON MERN WEB STACK

## MERN Web stack consists of following components:

- MongoDB: A document-based, No-SQL database used to store application data in a form of documents.

- ExpressJS: A server-side Web Application framework for Node.js.

- ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.

- Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

*This document describes the steps used to implement MERN WEB STACK on an AWS EC2 instance. The screenshots taken during the process are included below in the order they were used to perform the setup.*

### Preparing prerequisites 

- I already have an AWS Account I use, but if you don't please create one as it is necessary for this project.

- Create an EC2 instance in AWS with Ubuntu Server.

## STEP 1 - BACKEND CONFIGURATION

- Update Ubuntu
```bash
sudo apt update
```
- Upgrade Ubuntu
```bash
sudo apt upgrade
```
![alt text](<IMAGE/image 1 [task3].png>)

- Get location of Node.js software from ubuntu repositories
```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```
![alt text](<IMAGE/image 2 [task3].png>)

install *Node.js* on the server
- Install Node.js with command below:
```bash
sudo apt-get install -y nodejs
```
![alt text](<IMAGE/image 3 [task3].png>)

**Note:** The command above installs both `nodejs` and `npm`. NPM is a package manager for Node like apt fpr ubuntu, it is used to install Node modules & packages and to manage dependency conflicts.

- Verify the `node` installation with the command below:
```bash
node -v
```
- Verify the `npm` installation with the command below:
```bash
npm -v
```
![alt text](<IMAGE/image 4 [task3].png>)

### Application Code Setup

- Create a new directory for your TO-DO project:
```bash
mkdir Todo
```
- Run the command below to verify that the Todo directory is created with `ls` command
```bash
ls
```
- Change your current directory to the newly created one:
```bash
cd Todo
```
- Next you will use the command `npm init` to initialise your project, so that a new file named `package.json` will be created. This file will normally contain information about your application and the dependencies that it needs to run. Follow the prompts after running the command. You can press `Enter` several times to accept default values, then accept to write out the `package.json` file by typing `yes`.
```bash
npm init
```
![alt text](<IMAGE/image 5 [task3].png>)
Run the command `ls` to confirm that you have the package.json file created. Next we install `ExpressJS` and create Routes directory.


## INSTALL EXPRESSJS

- Install ExpressJS
```bash
npm install express
```
- Now create a file `index.js` with the command below:
```bash
touch index.js 
```
- Run ```ls``` to confirm that your index.js file is successfully created

- Install the dotenv module
```bash
npm install dotenv
```
- Open the index.js file with the command below
```bash
vim index.js
```
- Type the code below into it and save. Do not get overwhelmed by the code you see. For now, simply paste the code into the file.
```bash
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
- Notice that we have specified to use port 5000 in the code. This will be required later when we go on the browser.

- Now it is time to start our server to see if it works. Open your terminal in the same directory as your index.js file and type:
```bash
node index.js
```
![alt text](<IMAGE/image 6 [task3].png>)
If everything goes well, you should see Server running on port 5000 in your terminal.

- Now we need to open this port in EC2 Security Groups.
![alt text](<IMAGE/image 7 [task3].png>)

- Open up your browser and try to access your server’s Public IP or Public DNS name followed by port 5000:
http://PublicIP-or-PublicDNS:5000
![alt text](<IMAGE/image 8 [task3].png>)


## ROUTES

There are three actions that our To-Do application needs to be able to do:

- Create a new task
- Display list of all tasks
- Delete a completed task

Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE.

For each task, we need to create routes that will define various endpoints that the To-do app will depend on. So let us create a folder routes
```bash
mkdir routes
```
**Tip:** You can open multiple shell to connect to the same EC2

- Change directory to routes folder.
```bash
cd routes
```
- Now, create a file api.js with the command below:
```bash
touch api.js
```
- Open the file with the command below
```bash
vim api.js 
```
or 
```bash
nano api.js 
```
Copy below the code and paste them in the file.
```bash
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
![alt text](<IMAGE/image 9 [task3].png>)


## MODELS

**Now comes the interesting part, since the app is going to make use of Mongodb which is a NoSQL database, we need to create a model. A model is at the heart of JavaScript based applications, and it is what makes it interactive. We will also use models to define the database schema . This is important so that we will be able to define the fields stored in each Mongodb document. In essence, the Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties**

- Change directory back Todo folder with `cd ..` and install Mongoose
```bash
npm install mongoose
```
- Create a new folder models:
```bash
mkdir models
```
- Change directory into the newly created ‘models’ folder with:
```bash
cd models
```
- Inside the models folder, create a file and name it todo.js with the command below:
```bash
touch todo.js
```
- Open the file created with `vim todo.js` then paste the code below in the file:
```bash
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
![alt text](<IMAGE/image 10 [task3].png>)

- Now we need to update our routes from the file `api.js` in ‘routes’ directory to make use of the new model. In Routes directory, open `api.js` with `vim api.js`, delete the code inside with `:%d `command and paste the code below into it then save and exit with `:wq`.
```bash
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
![alt text](<IMAGE/image 11 [task3].png>)
**NOTE**: i used the cat command to check if the content i updated in that file was a success, it shows that means it worked.


## MONGODB DATABASE

- We need a database where we will store our data. For this we will make use of MongoDB database as a service solution (DBaaS).

- So to make life easy, you will need to sign up for a shared clusters free account, which is ideal for our use case. Sign up here: [MongoDB](https://www.mongodb.com/). Follow the sign up process, select AWS as the cloud provider, and choose a region near you.

- Create a cluster ( build a cluster ) and follow the steps in the images below
![alt text](<IMAGE/image 12 [task3].png>)

![alt text](<IMAGE/image 13 [task3].png>)

- incase you didn't see the "Allow Access From Anywhere" option, add is manually by yourself just as in the image below.
![alt text](<IMAGE/image 14 [task3].png>)

- incase you didn't see the "Allow Access From Anywhere" option, add is manually by yourself just as highlighted in the image below:

![alt text](<IMAGE/image 15 [task3].png>)

- Create a MongoDB database and collection

![alt text](<IMAGE/image 16 [task3].png>)

![alt text](<IMAGE/image 17 [task3].png>)

![alt text](<IMAGE/image 18 [task3].png>)

- In the `index.js` file, we specified `process.env` to access environment variables, but we have not yet created this file. So we need to do that now.

- Create a file in your Todo directory and name it `.env` by running the command below:
```bash
touch .env
```
```bash
vi .env
```
- Add the connection string to access the database in it, just as below:
```bash
DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
```
- Ensure to update `username`, `password`, `network-address` and `database` according to your setup

- Here is how to get your connection string:

![alt text](<IMAGE/image 19 [task3].png>)

![alt text](<IMAGE/image 20 [task3].png>)

![alt text](<IMAGE/image 21 [task3].png>)

- Now we need to update the `index.js` to reflect the use of `.env` so that `Node.js` can connect to the database.

- Simply delete existing content in the file, and update it with the entire code below by using vim or nano
```bash
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
![alt text](<IMAGE/image 22 [task3].png>)

- **NOTE**: Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the index.js application file.

- Start your server using the command:

`node index.js` You shall see a message ‘Database connected successfully'

![alt text](<IMAGE/image 23 [task3].png>)

- **TESTING OUR BACKEND CODE**

- So far, we have built the backend of our To-Do application. This backend handles things like saving tasks, reading tasks, updating them, and deleting them from the database. We have also successfully connected our app to a database.

- However, we do not have a frontend yet. That means there is no website or user interface where a user can click buttons or type tasks.

- Because of this, we still need a way to check if our backend is working correctly during development.

- To do that, we use a tool called an `API development client`. This type of tool allows us to send requests (like GET, POST, PUT, DELETE) directly to our backend and see the responses, without needing a frontend.

- In this project, we will use `**Postman**`

- Click the link below to downnload Postman and install postman on your machine.

[Postman download](https://www.postman.com/downloads)

- Click [HERE](https://www.youtube.com/watch?v=FjgYtQK_zLE) to learn how perform [CRUD operartions](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) on Postman.

- **Note**: Make sure your set header key `Content-Type` as `application/json`

![alt text](<IMAGE/image 24 [task3].png>)

- Create a POST request to the API http://PublicIP-or-PublicDNS:5000/api/todos. This request sends a new task to our To-Do list so the application could store it in the database.

![alt text](<IMAGE/image 25 [task3].png>)

- Create a GET request to your API on http://PublicIP-or-PublicDNS:5000/api/todos. This request retrieves all existing records from out To-do application (backend requests these records from the database and sends it us back as a response to GET request).

![alt text](<IMAGE/image 26 [task3].png>)

## STEP 2 – FRONTEND CREATION

- Since we are done with the functionality we want from our backend and API, it is time to create a user interface for a Web client (browser) to interact with the application via API.

- To start out with the frontend of the To-do app, we will use the create-react-app command to scaffold our app.

- In the same root directory as your backend code, which is the Todo directory, run:
```bash
npx create-react-app client
```
![alt text](<IMAGE/image 27 [task3].png>)

- Install Concurrently. It is used to run more than one command simultaneously from the same terminal window.
```bash
npm install concurrently --save-dev
```
- If you there is any vulnerabilities, run the command below:
```bash
npm audit fix --force
```
- Install nodemon. It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.
```bash
npm install nodemon --save-dev
```
![alt text](<IMAGE/image 28 [task3].png>)

- In `Todo` folder open the `package.json` file. Change the highlighted part of the below screenshot and replace with the code below.
```bash
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
```
![alt text](<IMAGE/image 29 [task3].png>)

- Change directory to ‘client’
```bash
cd client
```
- Open the package.json file
```bash
vi package.json
```
- Add the key value pair in the package.json file "proxy": "http://localhost:5000".

![alt text](<IMAGE/image 30 [task3].png>)

- The whole purpose of adding the proxy configuration in number 3 above is to make it possible to access the application directly from the browser by simply calling the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos

- Now, ensure you are inside the Todo directory, and simply do:
```bash
npm run dev
```
![alt text](<IMAGE/image 31 [task3].png>)

- Your app should open and start running on localhost:3000


- **Important note: In order to be able to access the application from the Internet you have to open TCP port 3000 on EC2 by adding a new Security Group rule. You already know how to do it.**

- From your Todo directory run:
```bash
cd client
```
- move to the src directory
```bash
cd src
```
- Inside your src folder create another folder called components
```bash
mkdir components
```
- Move into the components directory with:
```bash
cd components
```
- Inside ‘components’ directory create three files `Input.js`, `ListTodo.js` and `Todo.js`. by running this command below:
```bash
touch Input.js ListTodo.js Todo.js
```
![alt text](<IMAGE/image 33 [task3].png>)

- Open Input.js file
```bash
nano Input.js
```
- Copy and paste the following
```bash
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
- To make use of Axios, which is a Promise based HTTP client for the browser and node.js, you need to cd into your client from your terminal and run yarn add axios or npm install axios.

- Move your directory to client and paste the command below:
```bash
npm install axios
```
![alt text](<IMAGE/image 34 [task3].png>)

- Go to ‘components’ directory
```bash
cd src/components
```
- After that open your ListTodo.js
```bash
nano ListTodo.js
```
or
```bash
vim listTodo.js
```
- in the **ListTodo.js** copy and paste the following code
```bash
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
```
![alt text](<IMAGE/image 35 [task3].png>)

- Then in your **Todo.js** file, write the following code
```bash
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
![alt text](<IMAGE/image 36 [task3].png>)

- We need to make little adjustment to our react code. Delete the logo and adjust our App.js to look like this. Move to the src folder `cd ..` Make sure that you are in the src folder and run
```bash
nano App.js
```
- Copy and paste the code below into it
```bash
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
![alt text](<IMAGE/image 37 [task3].png>)

- In the src directory open the App.css
```bash
nano App.css
```
or 
```bash
vim App.css
```
- Then paste the following code into App.css:
```bash
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
![alt text](<IMAGE/image 38 [task3].png>)

- In the src directory open the index.css
```bash
nano index.css
```
- Copy and paste the code below:
```bash
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
![alt text](<IMAGE/image 39 [task3].png>)

- Go to the Todo directory
```bash
cd ../..
```
- When you are in the Todo directory run:
```bash
npm run dev
```


- **Assuming no errors when saving all these files, our To-Do app should be ready and fully functional with the functionality discussed earlier: creating a task, deleting a task and viewing all your tasks.**
