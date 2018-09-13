---
title: "Admin Review Dashboard"
slug: admin-dashboard
---

Uh-oh - trolls have been posting stupid and repetitive reviews to your database, so your team is requesting an **admin dashboard** to review and quickly delete them.

# New Feature Branch

Following the best practice of using feature branches, we're going to create a new feature branch to add an admin dashboard.

```bash
$ git checkout -b admin-dash
$ git branch
```

# Admin Dashboard - The Route

Let's plan which route this admin dashboard should live under.

Remember, we want to stay resourceful but how is an admin dashboard resourceful?

What is an admin dashboard? Currently it will just be a list of reviews, right? That means it should live here: `/reviews` and the code should be in `reviews.js`. Well maybe, or it could live here `/admin` and we could make a new controller called `admin.js`.

Which is better? To answer this, let's ask ourselves some more questions and then make a recommendation to our team.

1. Will the admin dashboard someday have many resources in it? Probably b/c admin dashboards could be administrating anything in the site.
1. Will the admin dashboard need to be private? Definitely in the future (not so important now).
1. Will admins want to see that they are on the admin dashboard from the URL? Probably, Yes. It is easier to see where you are if it says `/admin` or `/admin-dashboard` in the url instead of `/reviews` or something wonky like `/reviews-admin-dashboard`.

Ok so we'll flex "resourceful" routing and use the `/admin-dashboard` route and stick it in a new controller called `admin.js`.

# Seeing the Reviews

The view in this case can just be a simple table. Bootstrap gives us a good one to use in our new `admin.handlebars` template:

```HTML
<!-- admin.handlebars -->

<h2>Reviews</h2>
<table class="table">
  <thead>
    <tr>
      <th scope="col">_id</th>
      <th scope="col">Title</th>
      <th scope="col">Description</th>
    </tr>
  </thead>
  <tbody>
    {{#each reviews}}
      <tr>
        <th scope="row">1</th>
        <td>{{this.title}}</td>
        <td>{{this.description}}</td>
      </tr>
    {{/each}}
  </tbody>
</table>
```

Now we need to make the controller route to render this template.

```js
// controllers/admin.js

const Review = require('./models/review')

modules.export = function (app) {

  // NEW Comment
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


# Deleting The Review

Now we just have to add a Delete link to each review so our admins can delete reviews that are do not pass muster for quality.

```HTML
<h2>Reviews</h2>
<table class="table">
  <thead>
    <tr>
      <th scope="col">_id</th>
      <th scope="col">Title</th>
      <th scope="col">Description</th>
      <th scope="col">Delete</th>
    </tr>
  </thead>
  <tbody>
    {{#each reviews}}
      <tr>
        <th scope="row">1</th>
        <td>{{this.title}}</td>
        <td>{{this.description}}</td>
        <td><a href="#" id="deleteReview" data-review-id=${this._id}>Delete</a></td>
      </tr>
    {{/each}}
  </tbody>
</table>
```



# Test Code and Git Commit

Test your code and commit it to the current branch (admin-dash).

Now merge with master and push to github and heroku.
