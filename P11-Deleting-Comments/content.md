---
title: "Adding a Route for Comments"
slug: adding-route-for-comments
---

Finally, since we are creating comments, we should also be able to delete them.

Remember that currently we do not have authentication so we'll just be letting any user create and delete comments. As we developed this project for a go live, we would probably add authentication and only allow people to delete their own comments!

# Deleting Comments

You need to define a route. Here is the list of the current routes with a new one added for the comment form at the bottom.

| URL              | HTTP Verb | Action  |
|------------------|-----------|---------|
| /                | GET       | index   |
| /reviews/new     | GET       | new     |
| /reviews         | POST      | create  |
| /reviews/:id     | GET       | show    |
| /reviews/:id/edit| GET       | edit    |
| /reviews/:id     | PUT/PATCH | update  |
| /reviews/:id     | DELETE    | destroy |
| /reviews/comments | POST      | create  |
| /reviews/comments/:id | DELETE      | destroy  |

We'll use the `:id` url parameter to look up the comment we want to destroy.

# What the User Sees: Making a Delete Link

As always, we start with what the users sees and does. So let's make a link to delete a comment.

We can't set an `<a>` tag's method (it is always GET) so we are going to use a form to submit a DELETE request to our delete action path. Let's add this link to the comments partial at: `views/partials/comment.handlebars`.

```HTML
<!-- views/partials/comment.handlebars -->

<div class="card">
  <div class="card-block">
    <h4 class="card-title">{{this.title}}</h4>
    <p class="card-text">{{this.content}}</p>
    <!-- Delete link -->
    <p><form method="POST" action="/reviews/comments/{{this._id}}?_method=DELETE">
        <button class="btn btn-link" type="submit">Delete</button>
    </form></p>
  </div>
</div>
```

# Adding the Destroy Route

Now we need a delete action route. After deleting the comment, it should redirect back to the parent review (`reviews-show`).

```js
// app.js
...
// DELETE
app.delete('/reviews/comments/:id', function (req, res) {
  console.log("DELETE comment")
  Comment.findByIdAndRemove(req.params.id).then((comment) => {
    res.redirect(`/reviews/${comment.reviewId}`);
  }).catch((err) => {
    console.log(err.message);
  })
})
```

Ok so now try deleting a comment.

# What Happened

So now we've completed all our user stories

1. Users can view all reviews (index)
1. Users can create a review (new/create)
1. Users can view one review (show)
1. Users can delete a review (destroy)
1. Users can edit a review (edit/update)
1. Users can comment on reviews (comments#create)
1. Users can delete comments (comments#destroy)

You used **Resource Based Development** and **Resourceful Routing** to build a review app. 
