# MEAN Stack Deployment to Ubuntu in AWS
Now, when you have already learned how to deploy **LAMP**, **LEMP** and **MERN** Web stacks - it is time to get yourself familiar with MEAN stack and deploy it to Ubuntu server.

### MEAN Stack is a combination of following components:
- [MongoDB](https://www.mongodb.com/) (Document database) - Stores and allows to retrieve data.
- [Express](https://expressjs.com/) (Back-end application framework) - Makes requests to Database for Reads and Writes.
- [Angular](https://angular.io/) (Front-end application framework) - Handles Client and Server Requests
- [Node.js](https://nodejs.org/en/) (JavaScript runtime environment) - Accepts requests and displays results to end user

In order to complete this project you will need an AWS account and a virtual server with Ubuntu Server OS.

If you do not have an AWS account - go back to [WEB STACK IMPLEMENTATION](https://github.com/samuelbartels20/web-stack-implementation) Step 0 to sign in to AWS free tier account ans create a new EC2 Instance of t2.nano family with Ubuntu Server 20.04 LTS (HVM) image. Remember, you can have multiple EC2 instances, but make sure you **STOP** the ones you are not working with at the moment to save available free hours.

**Hint**: In previous projects we used different tools to connect to an EC2 instance, but if you do not want to install or launch anything outside of AWS, you can open youc CLI straight from Web Console in AWS, like this:

![](./images/ec2.gif)

In this project, you are going to implement a simple Book Register web form using MEAN stack

### Install NodeJs
<span style="color:red">Node.js</span> is a JavaScript runtime built on Chrome’s V8 JavaScript engine. <span style="color:red">Node.js</span> is used in this project to set up the Express routes and AngularJS controllers.

Update ubuntu
```
sudo apt update
```
![](./images/update.png)

Upgrade ubuntu
```
sudo apt upgrade
```
![](./images/upgrade.png)

Install NodeJS
```
sudo apt install -y nodejs
```
![](./images/node.png)

###  Install MongoDB
MongoDB stores data in flexible, <span style="color:red">JSON-like</span> documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```
![](./images/key.png)

```
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

Install MongoDB
```
sudo apt install -y mongodb
```
![](./images/mongo.png)

Start The server
```
sudo service mongodb start
```

Verify that the service is up and running
```
sudo systemctl status mongodb
```
![](./images/verify.png)

Install [npm](https://www.npmjs.com) - Node package manager.
```
sudo apt install -y npm
```
![](./images/npm.png)

Install ‘[body-parser](https://www.npmjs.com/package/body-parser) package

We need ‘body-parser’ package to help us process JSON files passed in requests to the server.
```
sudo npm install body-parser
```

Create a folder named ‘Books’
```
mkdir Books && cd Books
```

In the Books directory, Initialize npm project
```
npm init
```
![](./images/init.png)

Add a file to it named server.js
```
touch  server.js
vi server.js
```

Copy and paste the web server code below into the server.js file.
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

![](./images/disc.png)

### Install [Express](https://expressjs.com/) and set up routes to the server
Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database.

We also will use [Mongoose](https://mongoosejs.com/) package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register.
```
sudo npm install express mongoose
```

In ‘Books’ folder, create a folder named apps
```
mkdir apps && cd apps
```

Create a file named routes.js
```
touch routes.js
vi routes.js
```

Copy and paste the code below into routes.js
```
var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
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
![](./images/res.png)