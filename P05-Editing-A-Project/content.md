---
title: "Edit Route: Editing and Updating a Resource"
slug: editing-a-project
---

# Edit Link

Edit Route

```js
// app.js

// EDIT
app.get('/projects/:id/edit', function (req, res) {
  Project.findById(req.params.id, function(err, project) {
    res.render('projects-edit', {project: project});
  })
})
```

Template

# DRY Code & Sub Templates


# Update Route

Update route

```js
// app.js

// UPDATE
app.put('/projects/:id', function (req, res) {
  Project.findAndUpdateById(req.params.id, req.body, function(err, project) {
    res.redirect('/projects/' + project._id);
  })
})
```
