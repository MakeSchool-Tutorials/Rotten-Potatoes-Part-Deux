---
title: "Delete Route: Destroying a Resource"
slug: deleting-a-review
---

So we've come to the end of our RESTful and Resourceful routes. Only one to go: Delete.

| URL              | HTTP Verb | Action  |
|------------------|-----------|---------|
| /                | GET       | index   |
| /reviews/new     | GET       | new     |
| /reviews         | POST      | create  |
| /reviews/:id     | GET       | show    |
| /reviews/:id/edit     | GET       | edit    |
| /reviews/:id     | PUT/PATCH | update  |
| /reviews/:id     | DELETE    | Destroy |

# What the User Sees

As always, we start with what the users sees and does. So let's make a link to delete a review.

We can't set an `<a>` tag's method (it is always GET) so we are going to use a form to submit a DELETE request to our delete action path.

```html
<!-- views/reviews-show.handlebars -->

<h1>{{review.title}}</h1>
<h2>{{review.movieTitle}}</h2>
<p>{{review.description}}</p>
<p><a href="/reviews/{{review._id}}/edit">Edit</a></p>
<!-- Delete button -->
<p><form method="POST" action="/reviews/{{review._id}}?_method=DELETE">
    <button type="submit">Delete</button>
</form></p>
```

Now we need a delete action route. After deleting the review, it should redirect to the home page (`reviews-index`).

```js
// app.js
...
// DELETE
app.delete('/reviews/:id', function (req, res) {
  console.log("DELETE review")
  Review.findByIdAndRemove(req.params.id).then((review) => {
    res.redirect('/');
  }).catch((err) => {
    console.log(err.message);
  })
})
```

We did it! All RESTful routes are complete! It ain't pretty though... let's work on that next.
