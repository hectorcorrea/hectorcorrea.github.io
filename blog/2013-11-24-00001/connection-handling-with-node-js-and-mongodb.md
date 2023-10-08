# Connection handling with Node.js and MongoDB
One of the things that trips most newcomers to Node.js and MongoDB is how different the process to connect to databases from Node.js is compared to other programming environments.

For example, the pseudo-code below is a typical example of how database connection is handled in most programming environments:

```code
db = driver.openDB("connection string")
data = db.find("some-query")
db.close()
// do something with the data
```

The general concept **in most programming environments** is to connect to the database only when you need to access data and to **disconnect from it as soon as you are done accessing it**. This process makes perfect sense in multi-threaded environments because there could be hundreds of concurrent threads trying to access the database and, unless you close your database connection, you might run out of connection handlers. Internally database drivers create and manage a connection pool to share a limited number of network connections with a potentially large number of clients. However, the client must close its connection in order for a connection from the pool to be available to other threads. 

**In Node.js** since there is only [one thread](http://blog.mixu.net/2011/02/01/understanding-the-node-js-event-loop/) executing JavaScript code there is **no need to close the database connection as soon as your request completes*. You can and should open the database connection once and leave it open for as long as your web site is running. This allows the next request to use an already opened connection rather than having to re-open the connection to MongoDB on every request. You could close the connection as soon as you are done using it, but at least with MongoDB, there is no real reason to do so. In fact it is recommended that you keep it open. The following quote is from [Christian Amor Kvalheim](https://groups.google.com/d/msg/node-mongodb-native/mSGnnuG8C1o/Hiaqvdu1bWoJ), one of the authors of the MongoDB driver for Node.js: 

> You open do MongoClient.connect once when your app boots up and reuse the db object. [...] 
> So open it once an reuse across all requests.

This means that the pseudo-code to do database access in with Node.js and MongoDB is as follow:

```code
// Connect *once* to the DB as soon as your program is loaded
db = driver.openDB("connection string")

// ...on every request..
// when you need access to the database assume is already opened
data = db.find("some-query")

// do something with the data
// and don't close the connection
```


## Problems with Keeping the Database Open

Keeping the database open means that you need to maintain an object around to reuse the connection. This is fine except that passing a *database object* around means that several layers of your application are now passing an object that they really shouldn't know anything about. For example, if your Node.js application is an Express.js application, you will probably connect to the database in your main *server.js* program, save the value to a *db* variable, pass this variable around to your *routes* which in turn will pass it to your *models*. This is doable but ugly.


## Handling Disconnects

The other issue to consider is what happens if you connect to the database successfully when your site first launches, but then you lose connectivity to the database. When this happens **the MongoDB driver for Node.js will hang** when trying to execute commands on this bad connection. It would be nice if the driver would timeout and let you know that the database is not longer available, but that's not how it works. I've played with some of the server parameters like [connectTimeoutMS and socketTimeoutMS](http://mongodb.github.io/node-mongodb-native/driver-articles/mongoclient.html#server-a-hash-of-options-at-the-server-level-not-supported-by-the-url) to no avail.

Fortunately, as indicated in this [StackOverflow post](http://stackoverflow.com/questions/18688282/handling-timeouts-with-node-js-and-mongodb) the MongoDB client for Node.js issues a **close event** as soon as it loses connectivity to the database. You can tap into this event an invalidate your existing connection object.


## mongoconnect Module

I've written a Node.js module called *mongoconnect* that you can use to make the handling of connections to MongoDB from Node.js a black box so that you don't have to worry about the issues that I've mentioned in this blog post. This module is just a wrapper around the native [MongoClient.connect](http://mongodb.github.io/node-mongodb-native/api-generated/mongoclient.html#mongoclient-connect) which means that you still get to use all the built-in functionality that the driver provides, but without having to worry about connect/disconnect issues.

To get started you can install this module from NPM.

```terminal
npm install mongoconnect
```

Once installed you call the `setup()` method to configure what database you will connect to. This is something that you will typically do in your main *server.js* program.

```code
// In your server.js
var mongoConnect = require('mongoconnect');
mongoConnect.setup('mongodb://localhost:27017/yourDB');
```

Once configured then you just need to call the `execute()` method anywhere that you need to execute a command against MongoDB. For example, if you need to find a list of documents in your *blogModel.js* file you can do the following:

```code
// In blogModel.js
var mongoConnect = require('mongoconnect');
exports.getAll = function(callback) {

  mongoConnect.execute(function(err, db) {

    if(err) {
      // could not connect (or reconnect) to the DB
      return callback(err);    
    }

    db.collection('blog').find().toArray(function(err, docs) {

      if(err) {
        // could not fetch data
        return callback(err);
      }
      // let our callback handle the data
      callback(null, docs);

    }); 

  });

};
```

The beauty of `mongoConnect.execute()` is that you don't have to worry about opening the database, checking if it has already been opened, or if the connection is still valid. The `execute()` method does this for you. By the time your code is executed you just need to check the *err* and *db* values passed to you. You can find more documentation about this module and [the entire source code in this GitHub repo](https://github.com/hectorcorrea/mongoConnect).

The way this module is written at this time **it only supports connecting to one single MongoDB database**. This is fine for most small applications but you might want to make changes to it if you need to connect to multiple databases...or shoot me and e-mail and I might do that for you :)