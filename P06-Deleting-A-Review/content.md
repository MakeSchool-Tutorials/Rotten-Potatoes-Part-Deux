---
title: "Delete Route: Destroying a Resource"
slug: deleting-a-review
---

So we've come to the end of our RESTful and Resourceful routes. Only one to go: Delete.

# What the User Sees

As always, we start with what the users sees and does. So let's make a link to delete a review.

We can't set an `<a>` tag's method (it is always GET) so we are going to use a form to submit a DELETE request to our delete action path.

```html
<!-- reviews-index.handlebars -->

<h1>Reviews</h1>
{{#each reviews}}
  <a href="/reviews/{{this._id}}">
    <h5>{{this.title}}</h5>
    <small>{{this.movieTitle}}</small>
  </a>
  <a href="/reviews/{{this._id}}/edit">Edit</a>
  <form method="POST" action="/reviews/{{this._id}}?_method='DELETE'">
    <button type="submit">Delete</button>
  </form>
{{/each}}
```

Now we need a delete action route. After deleting the review, it should redirect to the home page (`reviews-index`).

```js
// app.js
...
// DELETE
app.delete('/reviews/:id', function (req, res) {
  Review.findByIdAndRemove(req.params.id, function(err) {
    res.redirect('/');
  })
})
```

We did it! All RESTful routes are complete! It ain't pretty though... let's work on that next.
