---
title: "Push to Production with Heroku"
slug: push-to-heroku
---

Time to ship some code! Since we've built and styled our Rotten Potatoes app, let's show it to the world by putting it online. We'll use a service called [Heroku](https://www.heroku.com) that is free, but it does require a **credit card** to be on file.

# Sign Up For and Install Heroku

First you need to create a Heroku account at [heroku.com](https://www.heroku.com).

Then you will need to add the Heroku Command Line Interface (CLI) to your bash terminal so we can interact with the Heroku service via the terminal and git.

Next we'll have to initialize our project as a git repository...

# Pushing to Heroku using Github

Let's start by installing git if you don't already have it installed. And then initializing our project folder as a git repository. Then we can check the git status. We should see all our files as uncommitted and unstaged to commit.

```bash
$ brew install git
$ git init
$ git status
```

Now we can stage all the files to commit, then commit them adding a commit message, and then double check our status. We should see we have no files to commit bc we just committed them all.

```bash
$ git add .
$ git commit -m 'init'
$ git status
```

Now let's use the `heroku` command to create a heroku app and name it with "rotten-potatoes" and then our initials. So someone named Samantha Bee would be "rotten-potatoes-sb". This `heroku create` command will add our heroku app as a git remote repository that we will be able to push to using the git command `git push`. We can see our remote repos by using the command `git remote -v`.

```bash
$ heroku create rotten-potatoes-MY-INITIALS
$ git remote -v
```

Alright, now we can push our code to heroku and open our new website.

```bash
$ git push heroku master
$ heroku open
```

You are probably seeing an error screen right now! Bit of a let down maybe. But also a good lesson - **Things never work the first time**. Let's debug our issues.

# Heroku Logs

When ever you have an error or problem with Heroku, you can see the logs by running `heroku logs`.

```bash
$ heroku logs
```

If you want to see the logs to continually you can add `--tail` to this command.

```bash
$ heroku logs --tail
```

What error are we seeing in heroku now? What do we need to do?

# Adding a Production Database

It looks like the error is that we cannot connect to our mongodb database. That's because it is looking at the `'mongodb://localhost/rotten-potatoes'` URI, but that is on our local computer and heroku, which is remote, doesn't have access to that. So we have to add a mongodb heroku add-on called mLabs.

```bash
$ heroku addons:create mongolab:sandbox
```

Then we have to point to this production mongodb database URI in our `app.js` file.

```js
// app.js

var mongoose = require('mongoose');
mongoose.connect(process.env.MONGODB_URI || 'mongodb://localhost/rotten-potatoes');
```

Now if we try to open our heroku app what happens?

```bash
$ heroku open
```

# WooHoo!

Awwww yeah - you did it! You made your first RESTful and Resourceful app using Node.js, Express.js, and MongoDB!
