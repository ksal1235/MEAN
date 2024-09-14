# 🚀 Welcome to MEAN project!

# MEAN Stack Deployment to Ubuntu in AWS.

In this project, We are tasked to implement a web solution based on MEAN stack in AWS Cloud.

MEAN Web stack consists of following components:
1. Mongodb: A document-based, No-SQL database used to store application data in a form of documents.
2. Expressjs: A server side Web Application framework for Back-end application.
3. Angular (Front-end application framework) - Handles Client and Server Requests
4. Node.js (JavaScript runtime environment) - Accepts requests and displays results to end user.

##Step 0 - Preparing prerequisites:
In order to complete this project we will need an AWS account  and a virtual server with Ubuntu Server OS.

# Provisioning a new EC2 instance of t2.micro family with Ubuntu Server 24.04 LTS (HVM):

I have provision the Machine in Mumbai Region Because year to me. To Avoid the latency Issue:
![image](https://github.com/user-attachments/assets/d7828baf-36b6-44d5-a966-3b6b6ad2cd42)

Accessing the Machine Using Private Key:

    ssh -i .\taufeek.pem ubuntu@15.207.222.9

![image](https://github.com/user-attachments/assets/3e04ea97-17f4-4e11-b024-c803e7815c89)

Updating and Upgrading the machine Using Following Commands:

     sudo apt update && sudo apt upgrade -y

![image](https://github.com/user-attachments/assets/ed0415c3-dbb7-4972-9101-7f5bbbe3a452)


Step 1: Install NodeJs:

A) Add certificates

    sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
![image](https://github.com/user-attachments/assets/e619ae58-ec43-48b2-8d34-3a0af1fa4ba1)



B) Installing Node.js version 18 by adding the NodeSource repository and then using it to install Node.js.

      curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
![image](https://github.com/user-attachments/assets/835cc000-09fd-4d03-8857-ce8d70a85a18)


C) Now installing the nodejs and Checking the Version.

      sudo apt-get install -y nodejs
      node -v

![image](https://github.com/user-attachments/assets/d7a98a6b-b3b8-4ef3-b72d-1b25e1a63afe)

Step 2: '
  Install MongoDB: Non-relational databases that store unstructured or semi-structured data. They are designed to handle large-scale data and distributed systems.

Examples:
Document-based: MongoDB, Couchbase.

A. The command we using to adds a GPG key to our system to authenticate packages from a specific repository
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```
B. Add MongoDB Repository: Create a MongoDB list file in /etc/apt/sources.list.d/.
```
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```

C. Updating the machine Again

    sudo apt update

![image](https://github.com/user-attachments/assets/e5b8380f-1a5f-4eb0-9398-c3e6066fe81f)

D. Installing MongoDB
```
sudo apt install -y mongodb-org
```
![image](https://github.com/user-attachments/assets/60da9f89-81ab-4c22-9eba-fc58fd40ab01)

E. Start MongoDB: Start the MongoDB service and enable it to start automatically on boot:
```
sudo systemctl start mongod
sudo systemctl enable mongod
```

![image](https://github.com/user-attachments/assets/1e818af7-1127-4dd8-95a2-18c700217c02)

F. Check MongoDB Status: Verify that MongoDB is running.
```
sudo systemctl status mongod
```
![image](https://github.com/user-attachments/assets/73f8f322-e09b-4799-89a3-72b4076dccd8)

G. We need 'body-parser' package to help us process JSON files passed in requests to the server.
```
sudo npm install body-parser
```

Create a folder named 'Books'

    mkdir Books && cd Books

In the Books directory, Initialize npm project  
  
    npm init

Add a file to it named server.js
```
vi server.js

```
Have to add script to The web server code below into the server.js file.

```
var express = require('express');
var bodyParser = require('body-parser');
var app = express();

app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());

require('./apps/routes')(app);

app.set('port', 3300);

app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});

```

![image](https://github.com/user-attachments/assets/483bc8bd-0fd0-45d2-a769-4dc13e1762a9)


Step 3: Install Express and set up routes to the server:

- Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database.
- We Will use Mongoose package which provides a straight-forward, schema-based solution to model our application data. We will use Mongoose to establish a schema for the database to store data of our book register


      sudo npm install express mongoose

- In 'Books' folder, create a folder named apps

      mkdir apps && cd apps

- Create a file named routes.js

      vi routes.js

![image](https://github.com/user-attachments/assets/84e22133-bc5a-47cc-877d-7a84cc9b5d4c)


Have to add script to The web server code below into the routes.js file.

```
var Book = require('./models/book');

module.exports = function(app) {
    app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
            if (err) throw err;
            res.json(result);
        });
    });

    app.post('/book', function(req, res) {
        var book = new Book({
            name: req.body.name,
            isbn: req.body.isbn,
            author: req.body.author,
            pages: req.body.pages
        });
        book.save(function(err, result) {
            if (err) throw err;
            res.json({
                message: "Successfully added book",
                book: result
            });
        });
    });

    app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
            if (err) throw err;
            res.json({
                message: "Successfully deleted the book",
                book: result
            });
        });
    });

    var path = require('path');
    app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
    });
};

```

In the 'apps' folder, create a folder named models

    mkdir models && cd models

Create a file named book.js

      vi book.js

![image](https://github.com/user-attachments/assets/9706d1e1-4853-4cc4-8468-eeee52f067a5)

Have to add script to The web server code below into the book.js file.

```
var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';

mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);

var bookSchema = mongoose.Schema({
    name: String,
    isbn: { type: String, index: true },
    author: String,
    pages: Number
});

var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);

```
![image](https://github.com/user-attachments/assets/a1519f65-650f-4863-bbcc-0dd9a2c5d4f1)

# Step 4 - Access the routes with AngularJS

-  AngularJS provides a web framework for creating dynamic views in our web applications. In this project, we use AngularJS to connect our web page with Express and perform actions on our book register.

-  Change the directory back to 'Books'

        cd ../..

- Create a folder named public.

      mkdir public && cd public



- Add a file named script.js.

    vi script.js

![image](https://github.com/user-attachments/assets/48df60c7-c7b5-4b02-878f-2050c47986af)


```


var app = angular.module('myApp', []);

app.controller('myCtrl', function($scope, $http) {
    $http({
        method: 'GET',
        url: '/book'
    }).then(function successCallback(response) {
        $scope.books = response.data;
    }, function errorCallback(response) {
        console.log('Error: ' + response);
    });

    $scope.del_book = function(book) {
        $http({
            method: 'DELETE',
            url: '/book/:isbn',
            params: { 'isbn': book.isbn }
        }).then(function successCallback(response) {
            console.log(response);
        }, function errorCallback(response) {
            console.log('Error: ' + response);
        });
    };

    $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + '", "isbn": "' + $scope.Isbn + '", "author": "' + $scope.Author + '", "pages": "' + $scope.Pages + '" }';
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

- In 'public' folder, create a file named index.html

      vi index.html

-  Add below into index.html file.

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

- Change the directory back up to 'Books'
    
      cd ..

- Start the server by running this command:

      node server.js
    
- The server is now up and running, we can connect it via port 3300. You can launch a separate Putty.

      curl -s http://localhost:3300

![image](https://github.com/user-attachments/assets/a0f1e98e-4c3e-4508-afe4-c4ccee4081c1)


It shall return an HTML page, it is hardly readable in the CLI, but we can also try and access it from the Internet Using Public Ip and 3300 Port:


    http://15.207.222.9:3300/
    
![image](https://github.com/user-attachments/assets/3bbb7a94-a98d-4909-8acc-8029ab8f5d2d)

# Conclusion:-
Based on the details provided, the conclusion about the MEAN project could include a summary of the successful implementation of the web solution using the MEAN stack on the AWS Cloud. It could also mention the integration of Node.js, MongoDB, Express, and AngularJS, along with the deployment details and server configuration.  
