---
title: "Bootstrap An Express Project"
slug: bootstrap-an-express-project
---

As a young developer, its important to show off what you've made so people know what you can do. Having a portfolio website is a great way to keep track of what you've built and learned and be able to share it with other people. In this tutorial we are going to build a site that lets you track the projects you've built and things you've learned.

# Getting Started - Node.JS and `npm`

We're gonna jump right in to starting the new project and provide more context as new topics and concepts come up.

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

Use npm to initialize a Node project.

```bash
$ mkdir portfolio-app
$ cd portfolio-app
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

Instal Handlebars.js to your project.

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
$ .. # to go back up one more level to the root folder of your project
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
    <title>Example App</title>
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

#