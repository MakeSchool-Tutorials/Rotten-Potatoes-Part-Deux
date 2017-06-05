---
title: "Bootstrap An Express Project"
slug: bootstrap-an-express-project
---

You might know the website [Rotten Tomatoes](rottentomatoes.com) where people can review movies. Well in this tutorial we are going to build Rotten Potatoes - our very own movie review website.

By finishing this tutorial you will continue to deepen your knowledge of Node.js and Express.js as well as master the internet-wide paradigms of RESTful and Resourceful routing. You will be Creating, Reading, Updating, and Deleting (CRUD) a single `Review` resource. You will also learn how to use a MongoDB document-based database with Express.js.

Let's jump in and bootstrap our Express.js app and add a templating engine.

# Getting Started - Node.JS and `npm`

We're gonna jump right in to starting the new review and provide more context as new topics and concepts come up.

For reference you can look at [ExpressJS's Getting Started](https://expressjs.com/en/starter/installing.html) docs

Open your computer's terminal and then...

If you don't already have Homebrew installed, install that first and then NodeJS & npm.

```bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
$ brew install node
```

> [info]
> Whenever you see the `$` in a command, that means it should be called in your computer's terminal. Remember: Don't include the `$` in your command.`

# Starting a Node.js & Express.js Project

Use npm to initialize a Node review.

```bash
$ mkdir rotten-potatoes
$ cd rotten-potatoes
$ npm init
# (hit enter for each option it asks for to select the default choice)
```

Now add Express.js and touch `app.js`

```
$ npm install express --save
$ touch app.js
```

And let's add some standard Express.js code to `app.js`

```js
var express = require('express')
var app = express()

app.get('/', function (req, res) {
  res.send('Hello World!')
})

app.listen(3000, function () {
  console.log('Portfolio App listening on port 3000!')
})
```

Make sure you've installed `nodemon` and run your server by running nodemon.

```bash
$ npm install nodemon -g
$ nodemon app.js
```

You should see "Portfolio App listening on port 3000!" output in your terminal. And if you enter `localhost:3000` into your browser, you should see "Hello World".


# Add Handlebars.js as a Templating Engine

Now let's add our templating engine Handlebars.js so our Express.js server can render _server-side HTML templates_.

Instal Handlebars.js to your review.

```bash
$ npm install express-handlebars --save
```

Initialize Handlebars.js in your app.

```js
// app.js
var exphbs  = require('express-handlebars');

app.engine('handlebars', exphbs({defaultLayout: 'main'}));
app.set('view engine', 'handlebars');
```

Create the `views`, `layouts` folders and `main.handlebars` and `home.handlebars` files.

```bash
$ mkdir views
$ cd views
$ mkdir layouts
$ touch home.handlebars
$ cd layouts
$ touch main.handlebars
$ .. # to go back up a level
$ .. # to go back up one more level to the root folder of your review
```

Now extend your _root route_ to render `home.handlebars`.

```js
// app.js

app.get('/', function (req, res) {
  res.render('home', {msg: 'Hello World!'});
})
```

```html
<!-- main.handlebars -->
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Rotten Potatoes</title>
</head>
<body>

  {{{body}}}

</body>
</html>
```

```html
<!-- home.handlebars -->
<h1>{{msg}}</h1>
```

Now when you visit `localhost:3000` you should still see "Hello World!" but now inside an `h1` tag.
