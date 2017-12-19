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

A MongoDB database allows you to save JavaScript Objects or JSON just as they are as **key value pairs**. In a MongoDB each object is called a single **document**, hence why this sort of database is called a **document-based database**. These documents are collected into groups called **collections**. We will save our review documents in a collection called "reviews".

MongoDB gives each document a unique identification number with the key **_id** (with an underscore). We can use that **_id** attribute to retrieve the whole document later. 

Working with MongoDB is like dropping clothes off at the drycleaners. They put a number on each piece of clothing and give you a ticket for each one. Later we can get that clothing back by matching our ticket to the right number.

If we created a new review like this to a MongoDB database:

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
```

You also need to create a folder to save your databases on your computer. 

```bash
$ mkdir -p /data/db
```

Now you need to start the MongoDB daemon.

```bash
$ mongod # SHORT FOR "MONGO DAEMON"
```

The command `mongod` should start MongoDB and now it will be accessible from your web server. Let's configure express to write to MongoDB using the npm library `mongoose`.

# Mongoose - An ODM - Object Document Mapper

Since our web server (Node.js) and our web framework (Express.js) are both written in JavaScript, our data is represented as JavaScript objects, but we have to save them as MongoDB documents. These look almost exactly the same, remember, but they are a tiny bit different. So we use a tool to map our JavaScript Objects to MongoDB documents. This tool is called an **ODM** or a **Object Document Mapper**. In Express.js, the most popular MongoDB ODM is called Mongoose.

First, install the mongoose npm module in your review.

```bash
$ npm install mongoose --save
```

Now initialize mongoose in `app.js` and connect to our database that we'll name after our app.

```js
// app.js

...

const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/rotten-potatoes', { useMongoClient: true });

...

```

Voila, you are connected to your database! But wait, you haven't written or read from it yet. Boring! In order to write and read data from your MongoDB database with Mongoose, you need to use what is called a **Model** - the "M" in **MVC** or the **Model View Controller** architecture of web server development.

# Making a Model

Let's add a model to our review.

```js
// app.js

const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/rotten-potatoes', { useMongoClient: true });

var Review = mongoose.model('Review', {
  title: String
});
```

> [info]
> Notice how the model is capitalized and singular (e.g. `Review`, `Article`, `User`)? This is a universal pattern across many web frameworks to designate models. It looks sorta like a class, right? Classes and models are similar except classes save in recent memory, and models are stored on the database.

Now that you have a model, you can use it to query your database for all the reviews that might be there. At first there won't be any reviews, so we should just expect the query to return an empty array.

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
  Review.find().then((reviews) => {
    res.render('reviews-index', {reviews: reviews});
  }).catch((err) => {
    console.log(err);
  })
})
```

A Mongoose model has many functions you can call on to query, create, and update documents in the MongoDB. We're going to use the `.find()`. The `find()` method returns a `Promise`. A `Promise` is an `Object` that represents represents a value that will be provided in the future. 

Call `.then()` and provide a function for the promise to call when it resolves. 

`Reviews.find().then((review) => { ...executed when the promise resolves... })`

Use `.catch()` and provide a function for the promise to call if the `Promise` is rejected. A `Promise` is rejected if it fails. 

`Reviews.find().then((review) => { ... }).catch((err) => { ...executed if the promise is rejected... })`

Refresh browser at `localhost:3000`. What do you see?

> [solution]
> You shouldn't see anything! :D - that's because there are no reviews in your database right now.

In the next lesson we will save a new review to our database. Onward!
