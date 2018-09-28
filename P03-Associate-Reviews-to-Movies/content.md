---
title: "Associate Reviews to Movies"
slug: associate-reviews-to-movies
---

Still on the same branch, we're going to keep going and associate reviews to movies and then list our reviews for each movie on the movie-show template.

# Review Movie Button

Let's start from what the user sees.

Since reviews will be associated with their parent movie, we cannot have a generic "New Review" button in the navbar. Making a new review must be associated only with its movie. So let's move our "New Review" button from the navbar into the `movie-show.handlebars` template. Let's put it in the upper right hand corner.

Also remember that we will be nesting reviews inside movies for our routes so look carefully at the resourceful structure of the new route to the reviews#new action.

```html
<!-- movies-show.handlebars -->
<div class="text-right">
    <a href="/movies/{{movie.id}}/reviews/new" class="btn btn-primary">Write Review</a>
</div>

...
```

That looks nice. Now to make it work we have to rewrite the route in the `reviews.js` controller.

```js

app.get('/movies/:movieId/reviews/new', (req, res) => {
  ...
})
```

Click the button and check that it redirects to the new form correctly.

# Adding movieId to Reviews - Hidden Input Field

Now we need to associate a review with its movie, which we track via its MovieDB id. So we need to pass this MovieDB id in with every review. We have two options for this.

1. Pass the movie id in with the url
1. Pass the movie id in with the form

There are pros and cons to either one. At the end of the day however, I like using a hidden input field to pass the movie attribute in the form. That way I don't have to write any special code in the controller to grab the id out of the url parameters later.

```js

app.get('/movies/:movieId/reviews/new', (req, res) => {

  render('reviews-new', { movieId: req.params.movieId })
})
```

Now we'll add a hidden field somewhere within the `<form>` tag block. We'll also need to update the action value in the form tag blog to send to our new nested resourceful routes.

```html
<!-- reviews-new.handlebars -->

<form action="/movies/{{movieId}}/reviews">

...

<input type="hidden" name="movieId" value="{{movieId}}">

...

</form>
```

Update your reviews#create route path, and add a console log to see if the req.body is coming through with the movieId.

```js
app.post('/movies/:movieId/reviews', (req, res) => {
  console.log(req.body)
})
```

If it is then you can complete the create logic and save the new review to the database.

# Adding Attribute to Review Model

But wait! If you save now, that will throw an error that our model is missing an attribute. There is no attribute called movieId in the `review.js` model. Let's add it.

```js
//review.js

...

, movieId        : { type: String, required: true }

...
```

Now if you submit the form it should work.

# Adding Reviews to the Movie Show Template

Don't forget about your reviews you already have. We're going to put those below the trailer.

First query them in the route.

```js
// movies.js

...

// SHOW
app.get('/movies/:id', (req, res) => {
  moviedb.movieInfo({ id: req.params.id }).then(movie => {

    // FIND THIS MOVIE'S REVIEWS
    Review.find({ movieId: req.params.id }).then(reviews => {
      // THEN RENDER THE MOVIES-SHOW TEMPLATE
      res.render('movies-show', { movie: movie, reviews: reviews });
    })

  }).catch(console.error)
})

```

We can copy and paste the code from our `reviews-show.handlebars` template. Let's also add an anchor tag so we can link to our reviews#show route and still view a single review and its comments.

```html
<!-- movies-show.handlebars -->

...

<h3>Reviews</h3>
{{#each reviews}}
<div>
  <h1><a href="/movies/{{movie.id}}/reviews/{{review._id}}">{{review.title}}</a></h1>
  <h2>{{review.movieTitle}}</h2>
  <p>{{review.description}}</p>
  <p><a href="/reviews/{{review._id}}/edit">Edit</a></p>
  <p><form method="POST" action="/reviews/{{review._id}}?_method=DELETE">
      <button class="btn btn-primary" type="submit">Delete</button>
  </form></p>
</div>
{{/each}}
```

Or we could actually abstract this into a **Partial** or **Subtemplate**. First by making a new template called `review.handlebars`

```html
<!-- review.handlebars -->

<div>
  <h1><a href="/movies/{{movie.id}}/reviews/{{review._id}}">{{review.title}}</a></h1>
  <h2>{{review.movieTitle}}</h2>
  <p>{{review.description}}</p>
  <p><a href="/reviews/{{review._id}}/edit">Edit</a></p>
  <p><form method="POST" action="/reviews/{{review._id}}?_method=DELETE">
      <button class="btn btn-primary" type="submit">Delete</button>
  </form></p>
</div>
```

Then we can use this partial inside our `{{#each}}` call.

```html
<!-- movies-show.handlebars -->

...

<h3>Reviews</h3>
{{#each reviews}}
  {{> review}}
{{/each}}
```


# Manual Testing

We don't have automated tests so you have to create a few reviews on a few movies and see that they display properly.

Can you edit and delete them?

You probably can since we didn't change those routes... but should we for consistency sake?

# CRUDing Reviews

So we've changed the new route to be nested for movies, let's update the other routes to be the same. Destroy, update, and create, should all remain the same.

| URL              | HTTP Verb | Action  | What it Does |
|------------------|-----------|---------|---------------|
| /                        | GET       | index   | See all movies |
| /movies/:id              | GET       | show    | See one movie |
| /movies/:id/reviews/:id  | GET       | show    | See one review |
| /movies/:id/reviews/new      | GET       | new    | New review |
| /movies/:id/reviews/:id/edit      | GET       | edit    | Edit a review |
| /movies/:id/reviews      | POST       | create    | Create one review |
| /movies/:id/reviews/:id      | PUT       | update    | Update one review |
| /movies/:id/reviews/:id      | DELETE       | destroy    | Destroy one review |

Wherever there are `/reviews` urls, they need to be updated to include the review's `movieId`.


# Test Again!

After changing all those routes, manually test CRUDing reviews again. If everything is working move on to the next step.

# Git Commit

If your code is working and you can fully CRUD all reviews, then commit the code to the current branch (movies).

There, you're ready to ship this big refactor to production.

Since the entire feature of adding movies to the app is finished, we can move on to merging this branch with master.

```bash
$ git checkout master
$ git merge movies
```

Now manually retest your code in master to see that everything is working well.

Now push to your remote github repo and Heroku.

```bash
$ git push origin master
$ git push heroku master
```
