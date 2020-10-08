---
title: "Admin Review Dashboard"
slug: admin-dashboard
---

## User Stories

1. ~~User can see recent movies from TheMovieDB~~
1. ~~User can see one Movie, including the poster and trailer~~
1. ~~User reviews are associated with the corresponding movie~~
1. ~~Users can add or delete comments without requiring a page refresh~~
1. **Users can access an admin dashboard that lists all reviews and allows them to delete them**
    1. **Build an Admin view that displays all reviews**
    1. **Build an Admin `/index` route**
    1. **Use `Axios` to handle deletion of reviews from the admin**
    1. **Build out the `/delete` route in the Admin controller**

Uh-oh - trolls have been posting stupid and repetitive reviews to your database, so your team is requesting an **admin dashboard** to review and quickly delete them.

# New Feature Branch

Following the best practice of using feature branches, we're going to create a new feature branch to add an admin dashboard.

>[action]
> Create a new `admin-dash` branch
>
```bash
$ git checkout -b admin-dash
```

# Admin Dashboard - The Route

Let's plan which route this admin dashboard should live under.

Remember, we want to stay resourceful but how is an admin dashboard resourceful?

What is an admin dashboard? Currently it will just be a list of reviews, right? Does that means it should live under `/reviews` and the code should be in `reviews.js`? Well maybe, or it could live in `/admin` and we could make a new controller called `admin.js`.

Which is better? To answer this, let's ask ourselves some more questions and then make a recommendation to our team.

1. Will the admin dashboard someday have many resources in it? Probably because admin dashboards could be administrating anything in the site.
1. Will the admin dashboard need to be private? Definitely in the future (not so important now).
1. Will admins want to see that they are on the admin dashboard from the URL? Probably, yes. It is easier to see where you are if it says `/admin` or `/admin-dashboard` in the url instead of `/reviews` or something wonky like `/reviews-admin-dashboard`.

Ok so we'll flex resourceful routing and use the `/admin-dashboard` route and stick it in a new controller called `admin.js`.

# Seeing the Reviews

The view in this case can just be a simple table. Bootstrap gives us a good one to use for our new `admin.handlebars` template:

>[action]
> Create `/views/admin.handlebars` by using the code below:
>
```HTML
<!-- admin.handlebars -->
>
<h2>Reviews</h2>
<table class="table">
  <thead>
    <tr>
      <th scope="col">ID</th>
      <th scope="col">Movie Title</th>
      <th scope="col">Title</th>
      <th scope="col">Rating</th>
      <th scope="col">Description</th>
    </tr>
  </thead>
  <tbody>
    {{#each reviews}}
      <tr>
        <th scope="row">{{this._id}}</th>
        <td>{{this.movieTitle}}</td>
        <td>{{this.title}}</td>
        <td>{{this.rating}}</td>
        <td>{{this.description}}</td>
      </tr>
    {{/each}}
  </tbody>
</table>
```

Now we need to make the controller route to render this template.

>[action]
> Create a new controller `/controllers/admin.js` and use the code below:
>
```js
// admin.js
>
const Review = require('../models/review')
>
module.exports = (app) => {
>
  // INDEX
  app.get('/admin', (req, res) => {
    Review.find()
      .then(reviews => {
          res.render('admin', { reviews: reviews });
      })
      .catch(error => {
        console.log(error);
      });
  });
}
```
>
> Remember to require this controller in `app.js`. Place this line near your other controller require statements:
>
```js
const admin = require('./controllers/admin')(app);
```

Try going to [http://localhost:3000/admin](http://localhost:3000/admin) and see if your table is rendering correctly and capturing all reviews. We don't want users to be able to easily access this page, so keep access to only the URL for now (we want to make it private, but that's a task for another day).

> [challenge]
> Buuut if you wanna save from typing, make a persistent link to the admin page on the `navbar`

# Deleting The Review

Now we just have to add a delete link to each review so our admins can delete reviews that do not pass our muster for quality.

>[action]
> Add a column for `delete` in `/views/admin.handlebars`, and also add the review's `id` to the `tr` for our client script to use shortly:
>
```HTML
<h2>Reviews</h2>
<table class="table">
  <thead>
    <tr>
      <th scope="col">ID</th>
      <th scope="col">Movie Title</th>
      <th scope="col">Title</th>
      <th scope="col">Rating</th>
      <th scope="col">Description</th>
      <th scope="col">Delete</th>
    </tr>
  </thead>
  <tbody>
    {{#each reviews}}
      <tr id="{{this._id}}">
        <th scope="row">{{this._id}}</th>
        <td>{{this.title}}</td>
        <td>{{this.movieTitle}}</td>
        <td>{{this.rating}}</td>
        <td>{{this.description}}</td>
        <td><button class="btn btn-link delete-review" data-review-id="{{this._id}}">Delete</button></td>
      </tr>
    {{/each}}
  </tbody>
</table>
```

Alright, we got the view wired up, now let's wire up our client script much in the same way we deleted comments:

> [action]
>
> Update `/public/javascript/scripts.js` to add a click handler for the Delete button on the admin page:
>
```js
// scripts.js
...
>
if (document.querySelectorAll('.delete-review')) {
    document.querySelectorAll('.delete-review').forEach((reviewElement) => {
        reviewElement.addEventListener('click', (e) => {
            console.log("click!")
            let reviewId = e.target.getAttribute('data-review-id')
>
            axios.delete(`/admin/reviews/${reviewId}`)
                .then(response => {
                    console.log("response: ",response);
                    review = document.getElementById(reviewId)
                    review.parentNode.removeChild(review); // OR review.style.display = 'none';
                }).catch((err) => {
                    console.log(err)
                    alert('There was an error deleting this review.')
                })
        })
    })
}
```

Finally, let's set up our admin controller with a `/delete` route of its own:

> [action]
>
> Add a `/delete` route to `/controllers/admin.js`:
>
```js
//admin.js
...
>
// DELETE
app.delete('/admin/reviews/:id', (req, res) => {
    Review.findByIdAndRemove(req.params.id).then((review) => {
        res.status(200).send(review);
    }).catch((err) => {
        console.log(err.message);
        res.status(400).send(err)
    })
})
```

Now try deleting a review from the admin, and make sure its deleted not only from the admin, but from the `movies-show` page as well.

Great work! By now you should feel more comfortable with the following, after having done them a few times now:

* **Using `AJAX` requests via `Axios` to do DELETE actions to the server!**
* **Creating client-side scripts using JavaScript**
* **Using a Feature Branch strategy**

# Now Commit
Make sure to test your code before committing!

>[action]
>
```bash
$ git add .
$ git commit -m 'admin for reviews'
$ git push --set-upstream origin admin-dash
```

# Now Merge

Now merge this branch with main, and push to github and heroku.

>[action]
> Merge this branch into main
>
```bash
$ git checkout main
$ git merge admin-dash
```
>
> Now manually retest your code in main to see that everything is working well.

# Now Push

Now push to your remote github repo and Heroku.

> [action]
> Push to main and Heroku:
>
```bash
$ git push origin main
$ git push heroku main
```

<!-- -->

>[challenge]
> What else can you build out on the admin to give it more functionality?

**Congratulations on finishing your new and improved Rotten Potatoes!!**
