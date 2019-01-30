---
title: "Associate Reviews to Movies"
slug: associate-reviews-to-movies
---

## User Stories

1. ~~User can see recent movies from TheMovieDB~~
1. ~~User can see one Movie, including the poster and trailer~~
1. **User reviews are associated with the corresponding movie**
    1. **Allow users to review movie from the `movies-show` page**
    1. **Pass the `movieId` through the review form**
    1. **Update the `review` model**
    1. **Add Reviews to the Movie Show Template**
    1. **Test!**
1. Add 100% test coverage for all routes

Right now you can see all the movies, and you can see one movie, but what about your reviews? They still have a `movieTitle` attribute, but that won't connect them to the movieDB movies we are getting from the API.

If we want to connect movies to reviews, we need to **Associate** them via a new `movieId` attribute we add to reviews and set upon creation of a review. Then we'll be able to see all of a movie's reviews on the `movie-show` page.

Still on the _same branch,_ we're going to keep going and associate reviews to movies and then list our reviews for each movie on the `movie-show` template.

# Review Movie Button

Even though we know that eventually we'll need to add a `movieId` attribute to the review model, let's not start with the model, let's follow the practice of **User Centered Development** and start from what the user sees.

Since reviews will be associated with their parent movie, we cannot have a generic "New Review" button in the `navbar` any more. Making a new review must be associated only with its movie. So let's move our _New Review_ button from the `navbar` into the `movie-show.handlebars` template. Let's put it in the upper right hand corner of the `movie-show` template instead.

We also need to keep track of the movie's ID, so let's nest the Review resource routes into movies. e.g. `POST /movies/:id/reviews`, `GET /movies/:movieId/reviews/:id`.

>[action]
> Update the `/views/movies-show.handlebars` template to include the _New Review_ button. Place this snippet right below the `<div class="row">`:
>
```html
<!-- movies-show.handlebars -->
>
<div class="row">
[bold]<div class="text-right">
    <a href="/movies/{{movie.id}}/reviews/new" class="btn btn-primary">New Review</a>
</div>[/bold]
...
```
>
> Also remember to remove the _New Review_ button from the `navbar` partial

Now that looks nice!

Now click the button. It should break but in a predictable way. _A good engineer can predict what an error will be._ It should be a `404` because we haven't changed the `/reviews/new` route to match our new nested `movie/review` routes. To make it work we have to rewrite the path to the `/new` route in the `reviews.js` controller.

>[action]
> Update the path in the `/new` route in `/controllers/reviews.js`:
>
```js
// NEW
app.get('/movies/:movieId/reviews/new', (req, res) => {
  ...
})
```

Click the button and check that it redirects to the new form correctly. `404` is gone! (See if you can predict errors before they happen.)

# Adding movieId to Reviews - Hidden Input Field

Now we need to associate a review with its movie, which we track via its MovieDB `id`. So we need to pass this MovieDB `id` in with every review. We have two options for this.

1. Pass the movie id in with the url
1. Pass the movie id in with the form

There are pros and cons to either one. For this tutorial, we will use a hidden input field to pass the movie attribute in the form. That way, we don't have to write any special code in the controller to grab the id out of the url parameters later.

>[action]
> First, update the `/new` route in `/controllers/reviews.js` to pass the `movieId` instead of `title`:
>
```js
// NEW
app.get('/movies/:movieId/reviews/new', (req, res) => {
  [bold]res.render('reviews-new', { movieId: req.params.movieId }) [/bold]
})
```

Now we'll add a hidden field somewhere within the `<form>` tag block. We'll also need to update the action value in the form tag block to send to our new nested resourceful routes.

>[action]
> Update the `action` value and add a hidden `input` to `/views/reviews-new.handlebars`:
>
```html
<!-- views/reviews-new.handlebars -->
...
>
<form method="POST" action="/movies/{{movieId}}/reviews">
>
...
>
    <input type="hidden" class="form-control" name="movieId" value="{{movieId}}">
</form>
```

Update your `/reviews/create` route path, and add a console log to see if the `req.body` is coming through with the `movieId`.

>[action]
> Update the `/create` route in `/controllers/reviews.js` to the following:
>
```js
// CREATE
app.post('/movies/:movieId/reviews', (req, res) => {
  console.log(req.body)
})
```

If the console log is showing correctly,  then you can complete the create logic and save the new review to the database.

>[action]
> Update the body of the `/create` route in `/controllers/reviews.js` to the following:
>
```js
// CREATE
app.post('/movies/:movieId/reviews', (req, res) => {
    Review.create(req.body).then((review) => {
        console.log(review);
        res.redirect(`/movies/${review.movieId}`); // Redirect to movies/:id
    }).catch((err) => {
        console.log(err.message);
    })
})
```

# Adding Attribute to Review Model

But wait! Another error prediction opportunity.

If you save now, the model will throw an error because our model is missing an attribute for `movieId`. There is no attribute called `movieId` in the `Review` model. Let's add it.

>[action]
> Add a `movieID` attribute to the `models/review.js` model:
>
```js
//review.js
...
const reviewScheme = new mongoose.Schema(
    {
        ...
        movieId: { type: String, required: true }
    },
    ...
);
...
```

This attribute is `required` because we don't want any reviews to be orphaned without a movie.

Refresh your browser and try to submit the form. It should work now!

# Adding Reviews to the Movie Show Template

Now that we've associated reviews with their movie, we have to display those below the trailer.

First, we need to query for the reviews in the `/movies/show` route in `/controllers/movies.js`:

>[action]
> Update your `/movies/show` route in `/controllers/movies.js` to the following:
>
```js
// movies.js
>
...
// SHOW
app.get('/movies/:id', (req, res) => {
    moviedb.movieInfo({ id: req.params.id }).then(movie => {
        moviedb.movieTrailers({ id: req.params.id }).then(videos => {
            movie.trailer_youtube_id = videos.youtube[0].source;
            console.log('VIDEOS.TRAILER_YOUTUBE_ID', movie.trailer_youtube_id);
            // FIND THIS MOVIE'S REVIEWS
            Review.find({ movieId: req.params.id }).then(reviews => {
                // THEN RENDER THE MOVIES-SHOW TEMPLATE
                res.render('movies-show', { movie: movie, reviews: reviews });
            }).catch(console.error);
        }).catch(console.error);
    }).catch(console.error);
})
```
>
> Notice we used `Review`, so make sure to require it at the top of `controllers/movies.js` as well:
>
```js
// movies.js
const Review = require('../models/review');
...
```

We can reuse a lot of our `/views/reviews-show.handlebars` template to show the reviews. Let's abstract this template into a new **Partial** or **Subtemplate** called `review.handlebars`. Let's also add an anchor tag so we can link to our `/reviews/show` route and still view a single review and its comments.

>[action]
> Create `views/partials/review.handlebars` using the below code:
>
```html
<!-- views/partials/review.handlebars -->
>
<div class="row">
    <div class="col-sm-6 col-sm-offset-3">
        <h2><a href="/movies/{{this.movieId}}/reviews/{{this._id}}">{{this.title}}</a></h2>
        <h3>{{this.movieTitle}}</h3>
        <p>{{this.description}}</p>
        <p>Rating (0-5 stars): {{this.rating}}</p>
        <p><a href="/reviews/{{review._id}}/edit">Edit</a></p>
        <p>
            <form method="POST" action="/reviews/{{this._id}}?_method=DELETE">
                <button class="btn btn-primary" type="submit">Delete</button>
            </form>
        </p>
    </div>
</div>
```

Then we can use this partial inside an `{{#each}}` call within `/views/movies-show.handlebars`:

>[action]
> Update `/views/movies-show.handlebars` to include the reviews partial. Place it underneath the `div` for the trailer:
>
```html
<!-- movies-show.handlebars -->
>
...
>
<div class="col-sm-12">
    <h1 class="col-sm-6 col-sm-offset-3">Reviews</h1>
    {{#each reviews}}
        {{> review}}
    {{/each}}
</div>
```

Refresh your browser to see the changes!

# Fixing Our Routes

Notice though that our anchor tag we added to the review title doesn't navigate to our `review-show` template. Let's wire up that route:

>[action]
> Update the `/show` route in `/controllers/reviews.js` to use the route we're using in the anchor tag we just previously created:
>
```js
...
// SHOW
app.get('/movies/:movieId/reviews/:id', (req, res) => {
...
```

Next let's tweak our `reviews-show` template to navigate us back to the movie instead of to home

> [action]
> Update `/views/reviews-show.handlebars` to navigate to the associated movie instead of home:
>
```html
<!-- views/reviews-show.handlebars -->
>
<a href="/movies/{{review.movieId}}">Back to {{review.movieTitle}}</a>
...
```

Lastly, we need to make sure that when we Edit a review that it saves appropriately. Let's update our `/update` route (get it??)

>[action]
> Change the `res.redirect` call in the `/update` route in `controllers/reviews.js` to the following:
>
```js
// UPDATE
app.put('/reviews/:id', (req, res) => {
...
[bold]res.redirect(`/movies/${review.movieId}/reviews/${review._id}`)[/bold]
...
});
```

Try editing your reviews now!

One quick visual change, our `/delete` route still works, but it navigates us back to `/` after deletion. Let's change this to redirect to the movie page instead.

> [action]
> Update `res.redirect` line in the `/delete` route in `controllers/reviews.js` to the following:
>
```js
...
// DELETE
app.delete('/reviews/:id', function (req, res) {
...
[bold]res.redirect(`/movies/${review.movieId}`);[/bold]
...
});
```

Try deleting a review and make sure you end up back on the corresponding movie page.

# Manual Testing

All the changes we made broke our automated tests... :( sad panda.

So you have to test manually by creating a few reviews on a few movies and see that they display properly.

Can you edit and delete them?

You probably can since we fixed up those routes...but what about comments?

# CRUDing Reviews

Before we get to Comments, let's make sure Reviews are nice and tidy. We've changed some of the routes to be nested for movies, let's update the other routes to be the same.

>[action]
> Update all of the routes to match the below CRUD table. Be sure to update the routes in `/controllers/reviews.js`, AND the appropriate views that call on these routes:
>
> **Remember:** wherever there are `/reviews` urls, they need to be updated to include the review's `movieId`.
>
| URL              | HTTP Verb | Action  | What it Does |
|------------------|-----------|---------|---------------|
| /                        | GET       | index   | See all movies |
| /movies/:id              | GET       | show    | See one movie |
| /movies/:movieId/reviews/:id  | GET       | show    | See one review |
| /movies/:movieId/reviews/new      | GET       | new    | New review |
| /movies/:movieId/reviews/:id/edit      | GET       | edit    | Edit a review |
| /movies/:movieId/reviews      | POST       | create    | Create one review |
| /movies/:movieId/reviews/:id      | PUT       | update    | Update one review |
| /movies/:movieId/reviews/:id      | DELETE       | destroy    | Destroy one review |

# Test Again!

After changing all those routes, manually test CRUDing reviews again. If everything is working move on to the next step.

# Now Commit

>[action]
>
```bash
$ git add .
$ git commit -m 'Reviews associated to movies, correct CRUD Routes'
$ git push
```

# CRUDing Comments

You've gone through nesting the routes for Reviews and fixing the controllers and views appropriately.

Without instructions, try to do the same now for Comments!

>[action]
> Update all of the routes to match the below CRUD table. Be sure to update the routes in `/controllers/comments.js`, AND the appropriate views that call on these routes:
>
| URL              | HTTP Verb | Action  | What it Does |
|------------------|-----------|---------|---------------|
| /movies/:movieid/reviews/:reviewId/comments      | POST       | create    | Create one comment |
| /movies/:movieid/reviews/:reviewId/comments/:id      | DELETE       | destroy    | Destroy one comment |
>
> **Hint:** Remember to use those hidden `input`s to pass along information you need.
>
>**Hint 2:** In order to fully implement the `/delete` route, you're going to need to pass the `movieId` into `/views/partials/comment.handlebars` through `/views/reviews-show.handlebars`. To do this, you can pass it along as a variable to the form:
>
```html
<!-- views/reviews-show.handlebars -->
...
<!-- Show Comments -->
{{#each comments}}
  {{> comment comment=this movieId=../review.movieId}}
{{/each}}
```
>
> Notice that you'll also need to tell the partial what `this` refers to (`comment=this`). Make sure to update your `/views/partials/comment.handlebars` appropriately now that we've renamed `this` to `comment`.

Try to implement this yourself before checking with the solution below.

>[solution]
> **/views/reviews-show.handlebars**
>
```html
<!-- views/reviews-show.handlebars -->
...
<!-- Show Comments -->
{{#each comments}}
  {{> comment comment=this movieId=../review.movieId}}
{{/each}}
```
>
> **/views/partials/comment.handlebars**
>
```html
div class="card">
  <div class="card-block">
    <h4 class="card-title">{{comment.title}}</h4>
    <p class="card-text">{{comment.content}}</p>
    <!-- Delete link -->
    <p><form method="POST" action="/movies/{{movieId}}/reviews/{{comment.reviewId}}/comments/{{comment._id}}?_method=DELETE">
        <button class="btn btn-link" type="submit">Delete</button>
    </form></p>
  </div>
</div>
```
>
> **/views/partials/comment-form.handlebars**
>
```html
<!-- views/partials/comment-form.handlebars -->
>
<form action="/movies/{{review.movieId}}/reviews/{{review._id}}/comments" method="post">
  <input type="hidden" class="form-control" value="{{review._id}}" name="reviewId">
  <input type="hidden" class="form-control" name="movieId" value="{{review.movieId}}">
  <fieldset>
  ...
```
>
> **/controllers/comments.js**
>
```js
// comments.js
const Comment = require('../models/comment');
module.exports = (app) => {
>
    // CREATE Comment
[bold]    app.post('/movies/:movieid/reviews/:reviewId/comments', (req, res) => { [/bold]
        Comment.create(req.body).then((comment) => {
            console.log(comment)
[bold]            res.redirect(`/movies/${req.body.movieId}/reviews/${comment.reviewId}`);[/bold]
        }).catch((err) => {
            console.log(err.message);
        });
    });
>
    // DELETE
[bold]    app.delete('/movies/:movieId/reviews/:reviewId/comments/:id', function (req, res) {[/bold]
        console.log("DELETE comment")
        Comment.findByIdAndRemove(req.params.id).then((comment) => {
[bold]            res.redirect(`/movies/${req.params.movieId}/reviews/${comment.reviewId}`);[/bold]
        }).catch((err) => {
            console.log(err.message);
        })
    })
}
```


# Now Commit

>[action]
>
```bash
$ git add .
$ git commit -m 'Updated Comments CRUD'
$ git push
```

# Git Merge

If your code is working and you can fully CRUD all reviews, then you're ready to ship this big refactor to production!

Since the entire feature of adding movies to the app is finished, we can move on to merging this branch with master.

>[action]
> Merge this branch into master
>
```bash
$ git checkout master
$ git merge movies
```
>
> Now manually retest your code in master to see that everything is working well.

Now push to your remote github repo and Heroku.

> [action]
> Push to master and Heroku:
>
```bash
$ git push origin master
$ git push heroku master
```
