#### implementing a web solution based on the MERN stack in AWS Cloud [To deploy a simple To-Do application that creates To-Do lists]

> ### MERN Web stack consists of following components:
* MongoDB: A document-based, No-SQL database used to store application data in a form of documents.
* ExpressJS: A server side Web Application framework for Node.js.
* ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.
* Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

> ### Launch an EC2 instance
 
 ![MERN Project](https://github.com/sundoug01/darey.io_projects-/assets/28840209/fcd3b000-8c33-4e95-905d-dccca1d076db)
 
### Step 1

Update and upgrade Ubuntu

```
   sudo apt update
```
```
   sudo apt upgrade
```
Lets get the location of Node.js software from Ubuntu repositories.

- curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -

- Install Node.js with the command below

> sudo apt-get install -y nodejs

Verify the nodejs and npm version installation with the command below
```
 node -v 
 npm -v
```
<img width="333" alt="Nodejs and npm version" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/81b071da-3d6b-41cd-8ce0-c5c78ad0a9e1">

- Application Code Setup

* Create a new directory for your To-Do project:
```
 mkdir Todo
```
Now change your current directory to the newly created one:
```
 cd Todo
```
Next, you will use the command *npm init* to initialise your project, so that a new file named *package.json* will be created.
This file will normally contain information about your application and the dependencies that it needs to run
 
<img width="471" alt="npm init " src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/913386e6-bf58-4468-93ae-8ca877692eb8">

### Step 2
- ExpressJS installation 


To use express, install it using npm:
```
npm install express
```
Now create a file index.js with the command below
```
touch index.js
```
Run ls to confirm that your index.js file is successfully created

Install the dotenv module
```
npm install dotenv
```
Open the index.js file with the command below
```
vim index.js
```
Type the code below into it and save. 

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
If all is good, running the command below  should output:
```
 node index.js
```
<img width="290" alt="Nodejs running" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/682389d5-8789-4eb2-8efa-c292e665087c">

 Open port 5000 in EC2 security groups to access via browser. Open up your browser and try to access your server’s 
 Public IP or Public DNS name followed by port 5000:
 
```
  http://<PublicIP-or-PublicDNS>:5000
  ```
  ![ExpressJs](https://github.com/sundoug01/darey.io_projects-/assets/28840209/6f27255f-33c3-411e-99ea-c6f116dd0d33)
  
  ### Routes
  
 There are three actions that our To-Do application needs to be able to do:

- Create a new task
- Display list of all tasks
- Delete a completed task

For each task, we need to create routes that will define various endpoints that the To-do app will depend on. Create a folder routes
```
mkdir routes
```
Tip: You can open multiple shells in Putty or Linux/Mac to connect to the same EC2

Change directory to routes folder.
```
cd routes
```
Now, create a file api.js with the command below
```
touch api.js
```
Open the file with the command below
```
vim api.js
```
Copy below code in the file.
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
### Models

Now comes the interesting part, since the app is going to make use of Mongodb which is a NoSQL database, we need to create a model.

A model is at the heart of JavaScript based applications, and it is what makes it interactive.

We will also use models to define the database schema . This is important so that we will be able to define the fields stored in each Mongodb document.
In essence, the Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties

To create a Schema and a model, I will install mongoose which is a Node.js package that makes working with mongodb easier.

Change directory back Todo folder with cd .. and install Mongoose
```
npm install mongoose
```
Create a new folder models :
```
mkdir models
```
Change directory into the newly created ‘models’ folder with
```
cd models
```
Inside the models folder, create a file and name it todo.js
```
touch todo.js
```
Tip: All three commands above can be defined in one line to be executed consequently with help of && operator, like this:
```
mkdir models && cd models && touch todo.js
```
Open the file created with vim todo.js then paste the code below in the file:
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
```
### Step 3 
> MongoDB Database installation 

Follow the sign up process, select AWS as the cloud provider, and choose a region near you
[https://www.mongodb.com/atlas-signup-from-mlab](url)

Create a file in your Todo directory and name it .env.
```
touch .env
vi .env
```
Add the connection string to access the database in it, just as below:

![Connection string](https://github.com/sundoug01/darey.io_projects-/assets/28840209/8e38404c-f887-46c8-8f55-8b089163a916)

```
DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
 ```
Now we need to update the index.js to reflect the use of .env so that Node.js can connect to the database.

Simply delete existing content in the file, and update it with the entire code below.

Now, paste the entire code below in the file.
``` 
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 3000;

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
 Start your server using the command:
```
node index.js
``` 
<img width="351" alt="Connected to MogoDB" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/da7733fc-5f66-4a77-9108-96e543961143">

![MongoDB](https://github.com/sundoug01/darey.io_projects-/assets/28840209/5e358a13-3c07-4043-8008-e83d32900c7e)

Backend successfully configured, moving to testing it 

### Frontend creation : ReactJS 

In the same root directory as the backend code, which is the Todo directory, run:
```
 npx create-react-app client
 ```
This will create a new folder in your Todo directory called client, where you will add all the react code.

> Running a React App
Before testing the react app, there are some dependencies that need to be installed.

Install concurrently. It is used to run more than one command simultaneously from the same terminal window.
```
npm install concurrently --save-dev
```
Install nodemon. It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.
```
npm install nodemon --save-dev
```
In Todo folder open the package.json file. Change the script part and replace with the code below.
```
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```

Configure Proxy in package.json
Change directory to ‘client’
```
cd client
```
Open the package.json file
```
vi package.json
```
Add the key value pair in the package.json file *"proxy": "http://localhost:5000"*.
The whole purpose of adding the proxy configuration in number 3 above is to make it possible to access the application directly from the browser by simply calling the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos

Now, ensure you are inside the Todo directory, and simply do:
```
npm run dev
```
Your app should open and start running on localhost:3000

Important note: In order to be able to access the application from the Internet you have to open TCP port 3000 on EC2 by adding a new Security Group rule. 

Creating your React Components
One of the advantages of react is that it makes use of components, which are reusable and also makes code modular. For our Todo app, there will be two stateful components and one stateless component.
From your Todo directory run
```
cd client
```
move to the src directory
```
cd src
```
Inside your src folder create another folder called components
```
mkdir components
```
Move into the components directory with
```
cd components
```
Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js.
```
touch Input.js ListTodo.js Todo.js
```
Open Input.js file
```
vi Input.js
```
Copy and paste the following
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
To make use of Axios, which is a Promise based HTTP client for the browser and node.js, you need to cd into your client from your terminal and run yarn add axios or npm install axios.

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
npm install axios
```
Go to ‘components’ directory
```
cd src/components
```
After that open your ListTodo.js
```
vi ListTodo.js
```
In the ListTodo.js copy and paste the following code
```
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
Then in your Todo.js file you write the following code
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
We need to make little adjustment to our react code. 

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
Then paste the following code into App.css:
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
Go to the Todo directory
```
cd ../..
```
When you are in the Todo directory run:
```
npm run dev
```
<img width="1107" alt="Frontend deployed" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/ec9f8918-45df-4286-b741-76716bda3627">

![My Todo(s)](https://github.com/sundoug01/darey.io_projects-/assets/28840209/68a8e9a2-07d0-46c0-9f26-ba1e9fce1d12)
