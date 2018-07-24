---
title: "Create Route: Saving a New Resource"
slug: creating-a-review
---

Remember we are doing Resourceful and RESTful architecture to our MVC structured app. We've made some views in the `views` folder, instantiated a model, and put our controller logic (our routes) into the `app.js` file. As our model and controller logic grows we can move them into other files. For this app it is so simple, that we'll just leave all that logic in the `app.js` file.

As for the **Resourceful Routes**, we've created just one: the index action for our `Review` resource.

Remember the previous table that showed a hypothetical set of routes for service like Instagram.
Here it is again for review.

| URL              | HTTP Verb | Action  |
|------------------|-----------|---------|
| /reviews          | GET       | index   |
| /reviews/new      | GET       | new     |
| /reviews          | POST      | create  |
| /reviews/:id      | GET       | show    |
| /reviews/:id/edit | GET       | edit    |
| /reviews/:id      | PATCH/PUT | update  |
| /reviews/:id      | DELETE    | destroy |


What do the routes look like for the app your are currently building?


| URL              | HTTP Verb | Action  |
|------------------|-----------|---------|
| /                | GET       | index   |

Now we want to create two more for the new action, and the create action.

| URL              | HTTP Verb | Action  |
|------------------|-----------|---------|
| /                | GET       | index   |
| /reviews/new     | GET       | new     |
| /reviews         | POST      | create  |

# Linking to New Review Route

First things first - what does the user see?

The user will have to click "New Review" to create a new review. So let's put a link into the   `reviews-index` template.

```html
<!-- views/reviews-index.handlebars -->

<h1>Reviews</h1>

<a href="/reviews/new">New Review</a>

{{#each reviews}}
  <h2>{{this.title}}</h2>
  <small>{{this.movieTitle}}</small>
{{/each}}
```

When you click this link you should see a friendly error reminding you that we haven't made the route for `/reviews/new` yet.

![Cannot Find Template](assets/find-template.png)

Remember that errors are not bad. They are like sign posts that tell you what to do next. So let's add our route to this new path.

# New Review Form

Now we have to make a route to the `/reviews/new` path, and have it render a `reviews-new` template.

```js
// app.js

// NEW
app.get('/reviews/new', (req, res) => {
  res.render('reviews-new', {});
})
```

If we navigate our browsers to `/reviews/new` we'll get a friendly little error reminding us that we don't have a template called `reviews-new.handlebars` yet. So let's put that into our `views` folder.

```html
<!-- views/reviews-new.handlebars -->

<form method="POST" action="/reviews">
  <fieldset>
    <legend>New Review</legend>
    <!-- TITLE -->
    <p>
      <label for="title">Title</label><br>
      <input type="text" name="title" />
    </p>
  </fieldset>

  <!-- BUTTON -->
  <p>
    <button type="submit">Save Review</button>
  </p>

</form>
```

> [info]
> Notice a few things about our form. The form tag has an HTML attribute called `action` that has a value equal to the path we want our form to submit its data to. `/reviews` is the route to our create action that we will add to our server in the next step.

Now if you navigate to `/reviews/new` you should see our new form looking great.

# Review Create Action

If you try to submit our form now, we see the form sends a **POST** request to the url `/reviews`, but we have not made a route that detects post requests to that path. So we see our friendly error:

```
cannot POST to /reviews
```

Our server has no route called `/reviews` that accepts a POST HTTP method. So let's make one!

First, you need to get ready to accept form data using an npm module called `body-parser`

```bash
$ npm install body-parser --save
```

Now initialize the `body-parser` module in our `app.js` file.

```js
// app.js
...
// INITIALIZE BODY-PARSER AND ADD IT TO APP
const bodyParser = require('body-parser');

...
// The following line must appear AFTER const app = express() and before your routes!
app.use(bodyParser.urlencoded({ extended: true }));

...
// CREATE
app.post('/reviews', (req, res) => {
  console.log(req.body);
  // res.render('reviews-new', {});
})
```

`body-parser` gives us a new attribute of the `req` object called `req.body` and this will contain the form data. So when you submit your form you should see it log in in your terminal like this:

```
{ title: 'Creating a Review',
  description: 'a sample review description' }
```

# Using Form Data to Create a Review

So now let's use that form data to save a new review to our MongoDB database using our `Review` model. We'll use the method provided to us by Mongoose called `create()`. After we create the review, let's redirect to the root path to see our new review.

```js
// app.js

// CREATE
app.post('/reviews', (req, res) => {
  Review.create(req.body).then((review) => {
    console.log(review);
    res.redirect('/');
  }).catch((err) => {
    console.log(err.message);
  })
})
```

When you submit your form what do you see?

# Adding a New Attribute to our Model

The new action will be our form for making a new review. For now reviews just have one attribute `title`, but we can always add more. Let's add an attribute called `description` and another called `movieTitle` so we can write some more details about the review we've done.

First let's add what the user sees - the `reviews-new.handlebars` form input field.

```html
<!-- views/reviews-new.handlebars -->

<form method="POST" action="/reviews">
  <fieldset>
    <legend>New Review</legend>
    <!-- TITLE -->
    <p>
      <label for="review-title">Title</label><br>
      <input id="review-title" type="text" name="title" />
    </p>

    <!-- MOVIE TITLE -->
    <p>
      <label for="movie-title">Movie Title</label><br>
      <input id="movie-title" type="text" name="movieTitle" />
    </p>

    <!-- DESCRIPTION -->
    <p>
      <label for="description-text">Description</label><br>
      <textarea id="description-text" name="description" rows="10" /></textarea>
    </p>
  </fieldset>

  <!-- BUTTON -->
  <p>
    <button type="submit">Save Review</button>
  </p>

</form>

```

Next, let's add the `description` attribute to the `Review` model.

```js
// app.js

const Review = mongoose.model('Review', {
  title: String,
  description: String,
  movieTitle: String
});
```

You can create any attributes you like for your model and use various data types such as: `String`, `Number`, and `Date`.

Can you resubmit the form? What happens now?


# Now Commit

```bash
$ git add .
$ git commit -m 'Users can create reviews'
$ git push
```

# Extra Credit: Adding a Rating Attribute

Can you add a rating attribute that is a `Number`, and then use `<select>` and `<option>`'s elements to have a dropdown for users to select `0-5` a stars rating? You might have to google for an example to see how this works.
