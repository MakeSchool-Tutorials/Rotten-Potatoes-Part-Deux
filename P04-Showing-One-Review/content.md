---
title: "Show Route: See One Resource"
slug: showing-one-review
---

We are building out all the RESTful routes for our `Review` resource.

![RESTful Routes](assets/RESTful-routes.png)

We've already completed the index, new, and create actions.

Now let's setup the **show** action so we give each single review its own page and unique url path.

# Show One Review

Remember always start with what the user will see and do. So if we want to create the show action, we'll want to start by making a link to the review from our index action template. Our route has to follow the `/reviews/:id` structure.

MongoDB automatically creates an `_id` attribute on anything you save. So we can use that `_id` attribute for our `:id` in the route.

```html
<!-- reviews-index.handlebars -->

<h1>Reviews</h1>
{{#each reviews}}
  <a href="/reviews/{{this._id}}">
    <h5>{{this.title}}</h5>
    <small>{{this.movieTitle}}</small>
  </a>
{{/each}}
```

What happens if you click on one of those links? A friendly error! Let's do what it says and make the route.

```js
// app.js

...

// SHOW
app.get('/reviews/:id', function (req, res) {
  res.send('I\'m a review')
});
```

Now what happens if you go to that route?

# req.params & Review.findById()

Ok time to add a template with an actual `review` object!

```js
// app.js

...

// SHOW
app.get('/reviews/:id', function (req, res) {
  Review.findById(req.params.id).exec(function (err, review) {
    res.render('reviews-show', {review: review});
  })
});
```

Now if we go to the route, we'll see the error that no template `reviews-show` is found. Great! Let's make it.

```html
<!-- views/reviews-show.handlebars -->

<h1>{{review.title}}</h1>
<h5>{{review.movieTitle}}</h5>
<p>{{review.description}}</p>
```

Now what do you see? All the links to reviews should work now.

# Add a Back Link

This is good, the show action is working, but there is a bit of a problem. Once you are on the show action page, you can't get back home. Let's fix that by putting in a "Back" link.

```html
<!-- views/reviews-show.handlebars -->

<a href="/">Back to Home</a>

<h1>{{review.title}}</h1>
<h5>{{review.movieTitle}}</h5>
<p>{{review.description}}</p>

```

That's better. What else could we do now that we have this show route?

# Update the Create Action's Redirect

It makes sense from the user's perspective that after we create a new review, we should be automatically redirected to it, no? Let's change our create route to redirect to the show path.

```js
// app.js

// CREATE
app.post('/reviews', function (req, res) {
  Review.create(req.body, function(err, review) {
    res.redirect('/reviews/' + review._id);
  })
})
```

Now our user experience is getting very smooth, and our code is getting more and more complete. Onward!
