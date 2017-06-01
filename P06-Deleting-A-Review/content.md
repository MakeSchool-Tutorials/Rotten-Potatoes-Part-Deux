---
title: "Delete Route: Destroying a Resource"
slug: deleting-a-project
---


```js
// app.js

// DELETE
app.delete('/projects/:id', function (req, res) {
  Project.findByIdAndRemove(req.params.id, function(err) {
    res.redirect('/');
  })
})
```
