---
title: "Adding a MongoDB Database to Your App"
slug: adding-mongodb
---

# MongoDB - A NoSQL Database

**JSON** or **JavaScript Object Notation** is a way to organize data to transmit it across the internet. It looks the same as a JavaScript object. It is made up of pairs of **keys** and **values** separated by colons (`:`) and surrounded by curly braces (`{}`). The only difference between a JavaScript object and a JSON object is the **keys** are wrapped in strings. For example:

```js
// JS OBJECT
{
  title: "Great Review"
}
```

```js
// JSON OBJECT
{
  "title": "Great Review"
}
```

A MongoDB database allows you to save JavaScript Objects or JSON just as they are as **key value pairs**. In a MongoDB each object is called a single **document**, hence why this sort of database is called a **document-based database**. These documents are collected into groups called **collections**. So we will save our review documents in a collection called "reviews".

MongoDB gives each document a unique identification number with the key **_id** (with an underscore). We'll use that **_id** attribute to retrieve the whole document later. MongoDB is sorta like dropping clothes off at the drycleaners. They put a number on each piece of clothing and give you a ticket for each one. Later we can get that clothing back by matching our ticket to the right number.

So if we saved a new review like this to a MongoDB database:

```js
// JS OBJECT
{
  title: "A New Review"
}
```

Then it will save something like this:

```js
// MONGODB OBJECT
{
  _id: "507f1f77bcf86cd799439011",
  title: "A New Review"
}
```

# Installing MongoDB

Our web server is going to save documents in MongoDB, but MongoDB itself has to run on our computer's operating system - it isn't an npm module we load into our review. So let's install it.

```bash
$ brew update
$ brew install mongodb
# WE ALSO NEED TO CREATE A FOLDER TO SAVE OUR DATABASES ON OUR COMPUTER
$ mkdir -p /data/db
# NOW WE START THE MONGO PROGRAM, IT'S "DAEMON"
$ mongod # SHORT FOR "MONGO DAEMON"
```

The command `mongod` should start MongoDB and now it will be accessible from your web server. Let's configure express to write to MongoDB using the npm library `mongoose`.

# Mongoose - An ODM - Object Document Mapper

Since our web server (Node.js) and our web framework (Express.js) are both written in JavaScript, our data is represented as JavaScript objects, but we have to save them as MongoDB documents. Now these look almost exactly the same, remember, but they are a tiny bit different. So we use a tool to map our JavaScript Objects to MongoDB documents. This tool is called an **ODM** or a **Object Document Mapper**. In Express.js, the most popular MongoDB ODM is called Mongoose.

First, install the mongoose npm module in your review.

```bash
$ npm install mongoose --save
```

Now initialize mongoose in `app.js` and connect to our database that we'll name after our app.

```js
// app.js

var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/portfolio-app');
```

Voila, we are connected to our database, but we haven't written or read from it at all. Boring! In order to write and read data from our MongoDB database with Mongoose, we need to use what is called a **model** - the "M" in **MVC** or the **Model View Controller** architecture of web server development.

# Making a Model

Let's add a model to our review.

```js
// app.js

var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/portfolio-app');

var Review = mongoose.model('Review', {
  title: String
});
```

> [info]
> Notice how the model is capitalized and singular (e.g. `Review`, `Article`, `User`)? This is a universal pattern across many web frameworks to designate models. It looks sorta like a class, right? Classes and models are sorta similar except classes save in recent memory, and models are stored on the database.

Now that we have a model, we can use it to query our database for all the reviews we have. At first though there won't be an reviews, so we should just expect for the query to return an empty array.

# Our First MongoDB Query with a Mongoose Model

Let's return to our root path that displays our `reviews-index` template.

First let's comment out our `reviews` variable that we hard coded. We're gonna use the database now instead with the model `Review` we instantiated.

```js
// app.js

// var reviews = [
//   { title: "Great Review" },
//   { title: "Next Review" }
// ]

// INDEX
app.get('/', function (req, res) {
  Review.find(function(err, reviews) {
    res.render('reviews-index', {reviews: reviews});
  })
})
```

A Mongoose model has all kinds of functions we can call on it to query, create, and update documents in the MongoDB. We're going to use the `.find(callback())` function which takes just one argument which is a **callback function**. A callback function is just a function that runs once something else finishes. In this case the callback function runs after the database returns the data we're asking for. The callback function returns an error message (if there is an error) and an array of objects (if there is no error).

Refresh browser at `localhost:3000`. What do you see?

> [solution]
> You shouldn't see anything! :D - that's because there are no reviews in your database right now.

In the next lesson we will save a new review to our database. Onward!
