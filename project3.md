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
