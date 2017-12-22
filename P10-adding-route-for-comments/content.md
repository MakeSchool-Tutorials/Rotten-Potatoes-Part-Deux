---
title: "Adding a Route for Comments"
slug: adding-route-for-comments
---

Now it's time to add a route to handle comments.

# Defining the Comments Route

You need to define a route. Here is the list of the current routes with a new one added for the comment form at the bottom.

| URL              | HTTP Verb | Action  |
|------------------|-----------|---------|
| /                | GET       | index   |
| /reviews/new     | GET       | new     |
| /reviews         | POST      | create  |
| /reviews/:id     | GET       | show    |
| /reviews/:id     | DELETE    | Destroy |
| /reviews/comment | POST      | create  |

The Comment route uses the POST method since creating a Comment will be sending data and creating new resources.

## Seting the action on the Comment form

Get the form to call the new route by adding an action. Make sure to set the method as `post`, use the route defined above.

```HTML
<!-- views/partials/comment-form.handlebars -->

<form action="/reviews/comment" method="post">
  <fieldset>
    ...
```

## Adding the Review Id to the form

Every comment will need something to connect it to the Review it was written for.

## Add a hidden field

To do this you'll need to include the `_id` of the review with the form. One way you can accomplish this is using a hidden form field. A hidden field is an input tag that has a value that isn't visible in the browser.

Add a hidden field to the form and set the value to the id of the review:

```HTML
<!-- views/partials/comment-form.handlebars -->

<form action="/reviews/comment" method="post">
  <input type="hidden" value="{{review._id}}" name="reviewId">
  <fieldset>
    ...
```

The hidden field get's a value from the `review._id` and has a name of `reviewId`.

Submitting this form will include the id of the review along with the title and content input by the user writing the comment.

## Define a route  

Define a new route in express to handle this new form.

```JavaScript
...
// NEW Comment
app.post('/reviews/comment', (req, res) => {
  res.send('reviews comment')
})
...
```

This should print the message 'reviews comment' to the browser when the form is submitted. This is good test to check if everything is working so far.

## Comment Model

To save comments to the database you need a Comment model. You'll need to define a new model for Comments; `app.js` is getting pretty crowded it would be good to move your models into their own files and folders.

Make a new file and folder: `models/comment.js`

```JavaScript
const mongoose = require('mongoose')

const Comment = mongoose.model('Comment', {
  title: String,
  content: String
});

module.exports = Comment
```

This is very similar to the Review model created earlier. The first line imports `mongoose`. We need `mongoose` here to create the new model.

The middle section is similar to the Review model with the difference that the Comment model only saves a `title` and `content`.

The last line exports the `Comment` object by attaching it to `module.exports`. By doing this you can import `Comment` into any of your other files. Import `Comment` into `app.js`.

Add the following near the top of `app.js`.

```JavaScript
...
const Comment = require('./models/comment')
...
```

You can do the same thing with the Review object. Create a new file: `models/review.js`.

Move the Review model code from `app.js` by **cutting** and pasting it into `models/review.js`, then add extra code shown below.

```JavaScript
const mongoose = require('mongoose')

const Review = mongoose.model('Review', {
  title: String,
  description: String,
  movieTitle: String
});

module.exports = Review
```

Now import `models/review.js` into `app.js`. Add the following near the top of `app.js`.

`const Review = require('./models/review')`

## Adding a reference to a Review inside a Comment

Every Comment will need to have reference to a Review. This reference is the `_id` of the Review. Remember, id's are unique so every Review can ask for all Comments that reference it's id. Or conversely, any Comment will be able to refer to the Review the Comment was written for through the the review id it owns.

Open `models/comment.js` make the following changes.

```JavaScript
const mongoose = require('mongoose')
const Schema = mongoose.Schema

const Comment = mongoose.model('Comment', {
  title: String,
  content: String,
  reviewId: { type: Schema.Types.ObjectId, ref: 'Review' }
});

module.exports = Comment
```

On the second line you got a reference to the Mongoose Schema object. This Object helps Mongoose organize all of the models that it is using.

The second change was to add a new property: `reviewId`. The value of this property is a `ref` or reference to another Mongoose model, 'Review' in this case. This name must match the name given to the model in `models/review.js`:

```JavaScript
...
const Review = mongoose.model('Review', {
  ...
})
}
```

You can check your work by testing your project in the browser. Any errors should show in the terminal. Comments cannot be saved yet but, saving Reviews should still work.

Submitting a comment the browser should show the meesage: 'reviews comment' from `res.send('reviews comment')` from the post comment route in app.js.

Revisit the post comment route now. Open app.js and look for `app.post('/reviews/comment', ...)`. You need to create a new comment and then reload the page. Make the changes below:

```JavaScript
// NEW Comment
app.post('/reviews/comment', (req, res) => {
  Comment.create(req.body).then((comment) => {
    res.redirect('/reviews/' + comment.reviewId)
  }).catch((err) => {
    console.log(err.message)
  })
})
```

Test your work. Write a comment and submit the form. This should show the comment in the console: `console.log(comment)`. If there are any errors they should appear in the console.

## Loading Comments for a Review

You still can't see comments in the browser. Let's tackle that problem next.

This is a chicken and egg problem. We'd like to see comments but can't see any if they don't exist in the database. For this reason we chose to write the code that create comments first. Now we will write the code to display comments.

Open `app.js` and make these changes to the route that shows a single review by id.

```JavaScript
// SHOW
app.get('/reviews/:id', (req, res) => {
  const findReviews = Review.findById(req.params.id)
  const findComments = Comment.find({ reviewId: Object(req.params.id) })

  Promise.all([findReviews, findComments]).then((values) => {
    console.log(values)
    res.render('reviews-show', { review: values[0], comments: values[1] })
  }).catch((err) => {
    console.log(err.message)
  })
})
```

This is an interesting use of `Promise`! `Promise.all()` runs any number of asynchronous requests in parallel (at the same time). The code in the `then()` block is run when all of the requests have resolved. Values from the requests are returned in an array, `values` in the example above. Where `values[0]` is the first request, `values[1]` is the second request etc.

In the code block above there are two requests `[findReviews, findComments]` when these resolve `values[0]` will be an array if reviews, and `values[1]` will be an array of comments.

## Displaying comments

Now that you have an array of comments we need to display them.

A partial will be a good way to keep your code clean and organized. Create a new file `views/partials/comment.handlebars`.

```HTML
<!-- views/partials/comment.handlebars -->

<div class="card">
  <div class="card-block">
    <h4 class="card-title">{{this.title}}</h4>
    <p class="card-text">{{this.content}}</p>
  </div>
</div>
```

Now display comments using this partial in `views/reviews-show.handlebars`.

```HTML
<!-- views/reviews-show.handlebars -->

<a href="/">Back to Home</a>

    ...

    {{> comment-form}}

    <hr>

    <!-- Show Comments -->
    {{#each comments}}
      {{> comment}}
    {{/each}}

  </div>
</div>
```

Use an `{{#each ...}}` block to display one copy of the `comment.handlebars` partial for **each** item in the comments array. Remember the `>` in `{{> comment}}` signifies that `comment.handlebars` is a partial.

Test your work! Navigate to a single Review and add a comment. Submitting the comment should display a new comment below the Comment form.

## What just happened?

You created a relationship between two different document/records in your database. Reviews each have a unique id. By saving the id of a Review with a Comment comments can find the Review that they are are associated with. Reviews can also find all of the Comments that are associated with their id.

This is a one to many relationship. This an important concept in database design, and an important tool you will use when managing data. 
