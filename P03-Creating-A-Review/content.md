---
title: "Create Route: Saving a New Resource"
slug: creating-a-project
---

Remember we are doing Resourceful and RESTful architecture to our MVC structured app. We've made some views in the `views` folder, and instantiated a model put our controller logic (our routes) into the `app.js` file. As our model and controller logic grows we can move them into other files. For this app it is so simple, that we'll just leave all that logic in the `app.js` file.

As for the RESTful routes, we've created just one: the index action for our `Project` resource.

![RESTful Routes](assets/RESTful-routes.png)

Now we want to create two more for the new action, and the create action.

# Linking to New Project Route

First things first - what does the user see?

The user will have to click "New Project" to create a new project. So let's put a link into the   `projects-index` template.

```html
<!-- projects-index.handlebars -->

<h1>Projects</h1>

<a href="/projects/new">New Project</a>

{{#each projects}}
  <h3>{{this.title}}</h3>
{{/each}}
```

When you click this link you should see a friendly error reminding you that we haven't made the route for `/projects/new` yet.

![Cannot Find Template](assets/find-template.png)

# Adding a New Attribute to our Model

The new action will be our form for making a new project. For now projects just have one attribute `title`, but we can always add more. Let's add an attribute called `description` so we can write some more details about the project we've done.

Let's add the `description` attribute to the `Project` model.

```js
// app.js
var Project = mongoose.model('Project', {
  title: String,
  description: String
});
```

You can create any attributes you like for your model and use various data types such as: `String`, `Number`, and `Date`.

# New Project Form

Now we have to make a route to the `/projects/new` path, and have it render a `projects-new` template.

```js
// app.js

// NEW
app.get('/projects/new', function (req, res) {
  res.render('projects-new', {});
})
```

If we navigate our browsers to `/projects/new` we'll get a friendly little error reminding us that we don't have a template called `projects-new.handlebars` yet. So let's put that into our `views` folder.

```html
<!-- projects-new.handlebars -->

<legend>New Project</legend>
<form method="POST" action="/projects">
  <!-- TITLE -->
  <p>
    <label for="title">Title</label><br>
    <input type="text" name="title" />
  </p>

  <!-- DESCRIPTION -->  
  <p>
    <label for="description">Description</label><br>
    <textarea name="description" rows="10" /></textarea>
  </p>

  <!-- BUTTON -->
  <p>
    <button type="submit">Save Project</button>
  </p>
</form>
```

> [info]
> Notice a few things about our form. The form tag has an html attribute called `action` that has a value equal to the path we want our form to submit its data to. `/projects` is the route to our create action that we will add to our server in the next step.

Now if we navigate to `/projects/new` we should see our new form looking great.

# Project Create Action

If we try to submit our form now, we see the form sends a **POST** request to the url `/projects`, but we have not made a route that detects post requests to that path. So we see our friendly error:

```
cannot POST to /projects
```

Our server has no route called `/projects` that accepts a POST HTTP method. So let's make one!

But first, we need to get ready to accept form data using an npm module called `body-parser`

```bash
$ npm install body-parser --save
```


```js
// app.js

// INITIALIZE BODY-PARSER AND ADD IT TO APP
var bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({ extended: true }));

...

// CREATE
app.post('/projects', function (req, res) {
  console.log(req.body);
  // res.render('projects-new', {});
})
```

`body-parser` gives us a new attribute of the `req` object called `req.body` and this will contain the form data. So when you submit your form you should see it log in in your terminal like this:

```
{ title: 'Creating a Project',
  description: 'a sample project description' }
```

# Using Form Data to Create a Project

So now let's use that form data to save a new project to our MongoDB database using our `Project` model. We'll use the method provided to us by Mongoose called `create()`. After we create the project, let's redirect to the root path to see our new project.

```js
// app.js

// CREATE
app.post('/projects', function (req, res) {
  Project.create(req.body, function(err, project) {
    res.redirect('/');
  })
})
```

When you submit your form what do you see?
