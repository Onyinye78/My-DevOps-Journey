## Before we begin this project, ensure you have;
1. Aws account
2. create an EC2 instance of t2.micro family with Ubuntu Server 24.04 LTS.
3. Basic understanding of Javascript.
4. Basic understanding of Angular.
5. Basic understanding of Node.js
6. Basic understanding of MongoDB.
7. Basic understanding of Non-Relational Databases.


## INSTALL NODEJS

Node.js is a javascript runtime built on Chrome's V8 JavaScript engine. Node.js is used in this tutorial to set up the Express routes and AngularJS controllers.

Update ubuntu

```
sudo apt update
```

Upgrade ubuntu

```
sudo apt upgrade
```

Add certifications

```
  sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
```

```
 curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
 ```

 install NodeJS

 ```
 sudo apt install -y nodejs
```

## Install MongoDB

MongoDB stores data in flexible, JSON-LIKE documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, ISBN number, author and number of pages.

```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```

```
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

install MongoDB

```
sudo apt install -y mongodb
```

i experienced error while installing mongoDB and i have install gnupg and curl if they are not installed already from my terminal.

```
sudo apt-get install gnupg curl
```
after that i deleted the previous entries

```
sudo rm /etc/apt/sources.list.d/mongodb-org*.list
```

then i List the keys so that they can be deleted

```
sudo apt-key list
```

```
sudo apt-key del <key>
```

after that i added gpg keys 

```
 curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor
 ```

 Add mongodb repo

 ```
 echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
 ```

 Update package manager:

```
sudo apt update
```

Install mongodb

```
 sudo apt install -y mongodb-org
 ```

 Verify installation

 ```
 mongod --version
 ```

 After you do that above, your mongodb will fail to start, but that's easy to resolve
Solution to failing to start after you install it.
Create a new service file for MongoDB.

```
sudo nano /etc/systemd/system/mongodb.service
```


Add the following content to the file:

```
  [Unit]
  Description=MongoDB Database Server
  Documentation=https://docs.mongodb.org/manual
  After=network.target
  [Service]
  User=mongodb
  ExecStart=/usr/bin/mongod --config /etc/mongod.conf
  PIDFile=/var/run/mongodb/mongod.pid
  LimitNOFILE=64000
  TimeoutStopSec=60
  Restart=on-failure
  [Install]
  WantedBy=multi-user.target
```

Save and close the file Ctrl+O Enter Ctrl+X.
Reload the systemd daemon:

```
sudo systemctl daemon-reload
```

Then start mongod

```sudo systemctl start mongod
sudo systemctl status mongod
```

Install npm - Node package manager

```
sudo apt install -y npm
```

Check if node and npm are installed

```
node -v
npm -v
```

Install 'body-parser body-parser package

We need 'body-parser' package to help us process JSON files passed in requests to the server.

```
sudo npm install body-parser
```


## Install Express and set up routes to the server

Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database.

We also will use Mongoose package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register.

```
sudo npm install express mongoose
``` 

create a Books folder 

```
mkdir Books 
```

```
cd Books
```

In 'Books' folder, create a folder named apps

```
mkdir apps && cd apps
```

Create a file named routes.js

```   
nano routes.js
```

Copy and paste the code below into routes.js


```
   // Import necessary modules
const Book = require('./models/book');
const path = require('path');

module.exports = function(app) {

  // Get all books
  app.get('/book', async function(req, res) {
    try {
      let result = await Book.find({});
      res.json(result);
    } catch (err) {
      console.error(err);
      res.status(500).json({ error: 'Internal Server Error' });
    }
  });

  // Add a new book
  app.post('/book', async function(req, res) {
    try {
      const { name, isbn, author, pages } = req.body;
      const book = new Book({ name, isbn, author, pages });
      let result = await book.save();
      res.json({
        message: "Successfully added book",
        book: result
      });
    } catch (err) {
      console.error(err);
      res.status(500).json({ error: 'Internal Server Error' });
    }
  });

  // Delete a book by ISBN
  app.delete('/book/:isbn', async function(req, res) {
    try {
      let result = await Book.findOneAndRemove({ isbn: req.params.isbn });
      res.json({
        message: "Successfully deleted the book",
        book: result
      });
    } catch (err) {
      console.error(err);
      res.status(500).json({ error: 'Internal Server Error' });
    }
  });

  // Serve the index.html file for any other routes
  app.get('*', function(req, res) {
    res.sendFile(path.join(__dirname, '../public', 'index.html'));
  });
};
```

In the 'apps' folder, create a folder named models

```
mkdir models && cd models
```

Create a file named book.js

```
nano book.js
```

Copy and paste the code below into 'book.js'

```
      var mongoose = require('mongoose');

     var bookSchema = new mongoose.Schema({
       name: String,
       isbn: { type: String, index: true },
       author: String,
       pages: Number
     });

     module.exports = mongoose.model('Book', bookSchema);
```

## Access the routes with AngularJS

AngularJS provides a web framework for creating dynamic views in your web applications. In this tutorial, we use AngularJS to connect our web page with Express and perform actions on our book register.

Change the directory back to 'Books'

```
cd ../..
```

Create a folder named public

```
mkdir public && cd public
```

Add a file named script.js

```
nano script.js
```

Copy and paste the Code below (controller configuration defined) into the script.js file.

```
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```

In 'public' folder, create a file named index.html 

```
nano index.html
```

copy and paste the code below into index.html file.

```
<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>
```


Change the directory back up to 'Books'

```
cd ..
```

Start the server by running this command:

```
node server.js
```

The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what curl command returns locally.


```
curl -s http://localhost:3300
```

It shall return an HTML page, it is hardly readable in the CLI, but we can also try and access it from the Internet.

For this - you need to open TCP port 3300 in your AWS Web Console for your EC2 Instance.

Now you can access our Book Register web application from the Internet with a browser using Public IP address or Public DNS name.

Quick reminder how to get your server's Public IP and public DNS name:

You can find it in your AWS web console in EC2 details run                                      
```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4 for Public IP address 
```

```
curl -s http://169.254.169.254/latest/meta-data/public-hostname for Public DNS name.
```
Everything is working!

## SUMMARY
In this project, I successfully implemented a web solution using the MEAN stack (MongoDB, ExpressJS, Angular, NodeJS) on the AWS cloud platform. This project involved setting up an EC2 instance, installing and configuring the necessary components, and developing a full-fledged web application to manage a book registry.