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
- Expressjs installation 


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
Type the code below into it and save. Do not get overwhelmed by the code you see. For now, simply paste the code into the file.

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

For each task, we need to create routes that will define various endpoints that the To-do app will depend on. So let us create a folder routes
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
Copy below code in the file. (Do not be overwhelmed with the code)
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

I have the backend successfully configured, moving to testing it 

### Testing Backend Code without Frontend using RESTful API
- Using Postman to test 

