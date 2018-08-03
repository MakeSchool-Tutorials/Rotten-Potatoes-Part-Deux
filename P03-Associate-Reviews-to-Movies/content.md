---
title: "Associate Reviews to Movies"
slug: associate-reviews-to-movies
---

Still on the same branch, we're going to keep going and associate reviews to movies and then list our reviews for each movie on the movie show template.

# Review Movie Button

# Adding movieId to Reviews - Hidden Input Field

# Adding Reviews to the Movie Show Template

Don't forget about your reviews you already have. We're going to put those below the trailer.

First query them in the route.

```

```

```html
<h3>Reviews</h3>

```

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

# Git Commit

Test your code and commit it to the current branch (movies).

Since the entire feature of adding movies to the app is finished, we can move on to merging this branch with master.

```bash
$ git checkout master
$ git merge movies
```

Now manually retest your code in master to see that everything is working well.

Now push to your remote github repo and Heroku.
