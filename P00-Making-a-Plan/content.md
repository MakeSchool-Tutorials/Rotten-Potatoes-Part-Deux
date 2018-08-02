---
title: "Making a Plan"
slug: making-a-plan
---

At the beginning of any project, we always want to make a plan. And that plan comes in the form of **User Stories**.

Some of these user stories will require major refactoring of our app. to


# User Stories

For this tutorial here are the user stories we'll be developing:

1. User can see recent movies from TheMovieDB
1. User can see one Movie, including the poster and trailer
1. User reviews are associated with one movie
1. User review has a star rating
1. User does not have to reload the page to add a review or other form submissions (use Axios for PUT/POST/DELETE actions)
1. Add 100% test coverage for all routes


# GitHub Feature Branch



# The MovieDB



| URL              | HTTP Verb | Action  | What it Does |
|------------------|-----------|---------|---------------|
| /reviews          | GET       | index   | See all reviews |
| /reviews/new      | GET       | new     | See new review form |
| /reviews          | POST      | create  | Create a new review |
| /reviews/:id      | GET       | show    | See one review |
| /reviews/:id/edit | GET       | edit    | See an edit review form |
| /reviews/:id      | PATCH/PUT | update  | Update a review |
| /reviews/:id      | DELETE    | destroy | Delete a review |
