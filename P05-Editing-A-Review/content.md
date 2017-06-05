---
title: "Edit Route: Editing and Updating a Resource"
slug: editing-a-review
---

Now we're checking off the seven RESTful and Resourceful routes. But we're not done until all of them are complete.

* Index
* Create
* Show
* Edit
* Update
* Delete

Normally in a site like Rotten Potatoes, we would only want authors of reviews to have the permission to edit or delete a review. However, because we do not have authentication yet, we're just going to let anyone edit and delete reviews.

# Edit Link

So we want people to be able to edit and update reviews, so let's again start from the user's perspective. Edit and Update are similar to New and Create. First we need a link to the edit route that renders the `reviews-edit`, and then we submit that edit form to the update route which will redirect to the show action.

So let's make the edit link:

```html
<!-- reviews-index.handlebars -->

<h1>Reviews</h1>
{{#each reviews}}
  <a href="/reviews/{{this._id}}">
    <h5>{{this.title}}</h5>
    <small>{{this.movieTitle}}</small>
  </a>
  <a href="/reviews/{{this._id}}/edit">Edit</a>
{{/each}}
```

Ok now if we click that edit link, we'll see the route is not found. So let's make our edit action. The edit action is like the show action because we look up the `review` by its `_id` in the url parameter, but then we render the information in a template as editable form elements.

```js
// app.js
...
// EDIT
app.get('/reviews/:id/edit', function (req, res) {
  Review.findById(req.params.id, function(err, review) {
    res.render('reviews-edit', {review: review});
  })
})
```

And of course we'll need that `reviews-edit` template. This template is a bit weird for three reasons:

1. **PUT vs. POST** - Although our update action will be expecting a PUT HTTP action, HTML forms cannot take an action attribute of `PUT`. This makes no sense, but nevertheless we must find a sensible work around to HTML's shortcomings. So what we'll do is add `?_method=PUT` onto the end of the action, and then on the server we'll intercept this request and direct it towards our update action that takes a PUT HTTP request.
1. **`value=""`** - we are using the `value` html attribute to pass in the values of the review we are trying to edit.
1. **`<textarea>{{}}</textarea>`** - the `<textarea>` HTML tag does not have a `value` attribute, so its contents must go between its open and close tags.

```html
<!-- reviews-edit.handlebars -->

<legend>Edit Review</legend>
<form method="POST" action="/reviews?_method=PUT">
  <!-- TITLE -->
  <p>
    <label for="title">Title</label><br>
    <input type="text" name="title" value="{{review.title}}"/>
  </p>

  <!-- MOVIE TITLE -->
  <p>
    <label for="movieTitle">Movie Title</label><br>
    <input type="text" name="movieTitle" value="{{review.movieTitle}}" />
  </p>

  <!-- DESCRIPTION -->  
  <p>
    <label for="description">Description</label><br>
    <textarea name="description" rows="10" />{{review.description}}</textarea>
  </p>
  <!-- BUTTON -->
  <p>
    <button type="submit">Save Review</button>
  </p>
</form>

```

# Update Route

Now remember that we needed to intercept that POST request and make sure its processed as a PUT request so it goes to our update action. (this will work for our delete action later too!). We'll use the [method-override]((https://github.com/expressjs/method-override)) middleware.

```js
// app.js
var express = require('express')
var methodOverride = require('method-override')
var app = express()

// override with POST having ?_method=DELETE or ?_method=PUT
app.use(methodOverride('_method'))
```

Now we can create our update action and it will receive requests with a PUT method.

```js
// app.js
...
// UPDATE
app.put('/reviews/:id', function (req, res) {
  Review.findAndUpdateById(req.params.id,  req.body, function(err, review) {
    res.redirect('/reviews/' + review._id);
  })
})
```

# DRY Code & Sub Templates

Did you notice that the code of our `reviews-new` and `reviews-edit` have a lot of similarities? Pretty much everything inside the `form` tag is the same. Let's use a **Partial Template** to pull that code out into its own template.

First make a folder called `partials` inside the `views` folder. Now in that `partials` folder create the `reviews-form.handlebars`.

```html
<!-- views/partials/reviews-form.handlebars -->

<!-- TITLE -->
<p>
  <label for="title">Title</label><br>
  <input type="text" name="title" value="{{review.title}}"/>
</p>

<!-- MOVIE TITLE -->
<p>
  <label for="movieTitle">Movie Title</label><br>
  <input type="text" name="movieTitle" value="{{review.movieTitle}}" />
</p>

<!-- DESCRIPTION -->  
<p>
  <label for="description">Description</label><br>
  <textarea name="description" rows="10" />{{review.description}}</textarea>
</p>
<!-- BUTTON -->
<p>
  <button type="submit">Save Review</button>
</p>
```

And now we can use this partial to replace that information in both our new and edit templates.

```html
<!-- reviews-new.handlebars -->

<legend>New Review</legend>
<form method="POST" action="/reviews">
  {{> partials/reviews-form }}
</form>

```

```html
<!-- reviews-edit.handlebars -->

<legend>Edit Review</legend>
<form method="POST" action="/reviews?_method=PUT">
  {{> partials/reviews-form }}
</form>
```

Triumph! DRY code. (Don't Repeat Yourself)

# Extra: "Cancel" buttons

Sometimes people might start making a resource and then want to cancel. Can you add a "Cancel" button next to the "Save Review" button? What will it do? Where will it link to?
