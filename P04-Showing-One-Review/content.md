---
title: "Show Route: See One Resource"
slug: showing-one-review
---

We are building out all the **Resourceful Routes** for our `Review` resource.

| URL              | HTTP Verb | Action  |
|------------------|-----------|---------|
| /                | GET       | index   |
| /reviews/new     | GET       | new     |
| /reviews         | POST      | create  |
| /reviews/:id     | GET       | show    |

We've already completed the index, new, and create actions. Now you will add a show action that will display a single resource via it's id.

Now let's setup the **show** action so we give each single review its own page and unique url path.

# Show One Review

Remember always start with what the user will see and do. To create the show action, you will want to start by making a link to the review from our index action template. Your route has to follow the `/reviews/:id` structure.

MongoDB automatically creates an `_id` attribute on anything you save. So we can use that `_id` attribute for our `:id` in the route. This is called the **Url or Request Parameter** and we access it in Express using the `req.params` parameter inside a controller route.

```html
<!-- views/reviews-index.handlebars -->

<h1>Reviews</h1>

<a href="/reviews/new">New Review</a>

{{#each reviews}}
  <h2><a href="/reviews/{{this._id}}">{{this.title}}</a></h2>
  <small>{{this.movieTitle}}</small>
{{/each}}
```

What happens if you click on one of those links? A friendly error! Let's do what it says and make the route.

```js
// app.js

...

// SHOW
app.get('/reviews/:id', (req, res) => {
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
app.get('/reviews/:id', (req, res) => {
  Review.findById(req.params.id).then((review) => {
    res.render('reviews-show', { review: review })
  }).catch((err) => {
    console.log(err.message);
  })
})
```

Now if we go to the route, we'll see the error that no template `reviews-show` is found. Great! Let's make it.

```html
<!-- views/reviews-show.handlebars -->

<h1>{{review.title}}</h1>
<h2>{{review.movieTitle}}</h2>
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
// CREATE
app.post('/reviews', (req, res) => {
  Review.create(req.body).then((review) => {
    console.log(review)
    res.redirect(`/reviews/${review._id}`) // Redirect to reviews/:id
  }).catch((err) => {
    console.log(err.message)
  })
})
```

# Now Commit

```bash
$ git add .
$ git commit -m 'Users can see single reviews'
$ git push
```

Now our user experience is getting very smooth, and our code is getting more and more complete. Onward!
