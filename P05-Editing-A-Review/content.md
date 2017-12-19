---
title: "Edit Route: Editing and Updating a Resource"
slug: editing-a-review
---

Now we're checking off the seven RESTful and Resourceful routes. But we're not done until all of them are complete.

* Index
* Create
* Show
* **Edit**
* **Update**
* Delete

Normally in a site like Rotten Potatoes, we would only want authors of reviews to have the permission to edit or delete a review. However, because we do not have authentication yet, we're just going to let anyone edit and delete reviews.

# Edit Link

We want people to be able to edit and update reviews, let's again start from the user's perspective. Edit and Update are similar to New and Create. First we need a link to the edit route that renders the `reviews-edit`, and then we submit that edit form to the update route which will redirect to the show action.

So let's make the edit link:

```html
<!-- views/reviews-show.handlebars -->

<h1>{{review.title}}</h1>
<h2>{{review.movieTitle}}</h2>
<p>{{review.description}}</p>

<p><a href="/reviews/{{review._id}}/edit">Edit</a></p>
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

<form method="POST" action="/reviews/{{review._id}}?_method=PUT">
  <fieldset>
    <legend>Edit Review</legend>
    <!-- TITLE -->
    <p>
      <label for="review-title">Title</label><br>
      <input id="review-title" type="text" name="title" value="{{review.title}}"/>
    </p>

    <!-- MOVIE TITLE -->
    <p>
      <label for="movie-title">Movie Title</label><br>
      <input id="movie-title" type="text" name="movieTitle" value="{{review.movieTitle}}" />
    </p>

    <!-- DESCRIPTION -->
    <p>
      <label for="review-description">Description</label><br>
      <textarea id="review-description" name="description" rows="10" />{{review.description}}</textarea>
    </p>
  </fieldset>
  <!-- BUTTON -->
  <p>
    <button type="submit">Save Review</button>
  </p>
</form>
```

# Update Route

Remember that you needed to intercept this POST request and make sure its processed as a PUT request so it goes to our update action. (this will work for our delete action later too!). We'll use the [method-override]((https://github.com/expressjs/method-override)) middleware.

```bash
$ npm install method-override --save
```
After importing the new package use `require('method-override')` to import it into your project. Add the following at the top of `app.js`.

`const methodOverride = require('method-override')`

Now add `methodOverride` as middleware after `const express = require('express')` but before your reoutes.

```js

const express = require('express')
const methodOverride = require('method-override')

...

const app = express()

...

// override with POST having ?_method=DELETE or ?_method=PUT
app.use(methodOverride('_method'))
```

Now you can create your update action and it will receive requests with a PUT method.

```js
// app.js
...
// UPDATE
app.put('/reviews/:id', (req, res) => {
  Review.findByIdAndUpdate(req.params.id, req.body).then((review) => {
    res.redirect('/reviews/' + review._id)
  }).catch((err) => {
    console.log(err.message)
  })
})
```

# DRY Code & Sub Templates

Did you notice that the code of our `reviews-new` and `reviews-edit` have a lot of similarities? Pretty much everything inside the `form` tag is the same. Let's use a **Partial Template** to pull that code out into its own template.

First make a folder called `partials` inside the `views` folder. Now in that `partials` folder create the `reviews-form.handlebars`.

```html
<!-- views/partials/reviews-form.handlebars -->

<fieldset>
  <legend>Edit Review</legend>
  <!-- TITLE -->
  <p>
    <label for="review-title">Title</label><br>
    <input id="review-title" type="text" name="title" value="{{review.title}}"/>
  </p>

  <!-- MOVIE TITLE -->
  <p>
    <label for="movie-title">Movie Title</label><br>
    <input id="movie-title" type="text" name="movieTitle" value="{{review.movieTitle}}" />
  </p>

  <!-- DESCRIPTION -->
  <p>
    <label for="review-description">Description</label><br>
    <textarea id="review-description" name="description" rows="10" />{{review.description}}</textarea>
  </p>
</fieldset>
```

And now we can use this partial to replace that information in both our new and edit templates.

```html
<!-- reviews-new -->

<form method="POST" action="/reviews">
  {{> reviews-form}}

  <!-- BUTTON -->
  <p>
    <button type="submit">Save Review</button>
  </p>

</form>
```

```html
<!-- reviews-edit.handlebars -->

<form method="POST" action="/reviews/{{review._id}}?_method=PUT">
  {{> reviews-form}}
  <!-- BUTTON -->
  <p>
    <button type="submit">Save Review</button>
  </p>
</form>
```

Triumph! DRY code. (Don't Repeat Yourself)

# Extra Credit: "Cancel" buttons

Sometimes people might start making a resource and then want to cancel. Can you add a "Cancel" button next to the "Save Review" button? What will it do? Where will it link to?
