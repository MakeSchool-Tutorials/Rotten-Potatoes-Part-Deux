---
title: "Show Route: See One Resource"
slug: see-one-project
---

We are building out all the RESTful routes for our `Project` resource.

![RESTful Routes](assets/RESTful-routes.png)

We've already completed the index, new, and create actions.

Now let's setup the show action so we give each single project its own page and unique url path.

# Show One Project

Link
Route
Template

# req.params & Project.findById()



# Add a Back Link

```html
<a href="/">Back</a>
```

# Update the Create Action's Redirect

Redirect from create

```js
// app.js

// CREATE
app.post('/projects', function (req, res) {
  Project.create(req.body, function(err, project) {
    res.redirect('/projects/' + project._id);
  })
})
```
