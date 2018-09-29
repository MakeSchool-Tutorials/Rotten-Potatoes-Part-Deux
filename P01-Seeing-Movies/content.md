---
title: "Seeing Movies"
slug: seeing-movies
---

# New GitHub Feature Branch

Git is for **versioning** your code so you can **revert** to older versions if you screw up. And it is for **sharing** code and **collaborating** with other developers. In order to do both of these better, Git offers a feature called **Branches**. A branch is a separate version of your code base that is different from other branches. So far you have always been coding on the **master** branch, but for this tutorial we are going to master using named **feature branches** to keep our features separate from master before we are ready to **merge** the two branches and then push master to github and production.

We want to add grouping reviews by movie to our review app, so let's make a feature branch called `movies`. We'll use the `get checkout -b BRANCH-NAME` command to make a new branch, and then `git branch` to see our branches.

```bash
$ git checkout -b movies
$ git branch
```

You should see you are in a new branch called `movies`.

# The MovieDB

We're going to use The MovieDB to search and view recent + popular movies.

[Here are the docs](https://developers.themoviedb.org/3/getting-started/introduction)

Head over, sign up, and [get an API key](https://developers.themoviedb.org/3/getting-started/introduction)

In Application URL just put "N/A", and in your Application Summary put "Building a website and using your API only for educational purposes. Thanks!"

# Movies Resource

We are going to create a **Movie** resource, but it won't have a model or a collection in our database, because the movies all live inside The MovieDB, but we will have a `movies.js` controller to house the Movie resource routes. Because we cant CREATE or UPDATE information in the MovieDB, we'll only need two movies routes `index` and `show`:


| URL              | HTTP Verb | Action  | What it Does |
|------------------|-----------|---------|---------------|
| /movies          | GET       | index   | See all movies |
| /movies/:id      | GET       | show    | See one movie |

So make that `controllers/movies.js` file and add it to your `app.js` with a `require()` statement like you added `reviews.js` and `comments.js`.

# Updating Root Route

When you go to Rotten Tomatoes, notice that the root route is not actually of reviews#index, it is of movies#index. We should do that too!

Let's comment out the `/` route in `reviews.js` and add a root route to our `movies.js` file

```js
// movies.js

module.exports = function(app) {

  app.get('/', (req, res) => {
    res.render('movies-index');
  });  

}
```

Now you'll need a `movies-index.handlebars` template with a little

```html
<h1>Hello Movies</h1>
```

Head over to your browser and check that your root route is now showing this `h1` tag.

# Calling our API

Now let's call the API.

A quick search of [npmjs.org](https://www.npmjs.com/package/moviedb-promise) reveals that there is a wrapper for the MovieDB API. Let's use it to simplify how we call the movieDB API.

```bash
$ npm install moviedb-promise --save
```

Now let's add it to our `movies.js` controller and then use it to request movies that are now playing.  Make sure to put in your API key into the initialization before calling any queries or you can bet it will throw an error saying that there is no valid API key.

```js
// controllers/movies.js

const MovieDb = require('moviedb-promise')
const moviedb = new MovieDb('your api key')

app.get('/', (req, res) => {
  moviedb.miscNowPlayingMovies().then(response => {
    res.render('movies-index', { movies: response.results });
  }).catch(console.error)
})
```

We used the `miscNowPlayingMovies()` method, but you can use any you find in the [moviedb-promise docs](https://www.npmjs.com/package/moviedb-promise) and the [Movies section of the MovieDB API docs](https://developers.themoviedb.org/3/movies/get-movie-details). Play around with a sample request with their awesome [API method docs and playground](https://developers.themoviedb.org/3/movies/get-now-playing).


And now we need to show the list of results in the template.

```html
<h1>Now Playing</h1>
{{#each movies}}
  <li>{{this.title}}</li>
{{/each}}
```

Here's the JSON data we have to play with:

```
{
  "vote_count": 329,
  "id": 353081,
  "video": false,
  "vote_average": 7.5,
  "title": "Mission: Impossible - Fallout",
  "popularity": 369.378,
  "poster_path": "/AkJQpZp9WoNdj7pLYSj1L0RcMMN.jpg",
  "original_language": "en",
  "original_title": "Mission: Impossible - Fallout",
  "genre_ids": [
    12,
    28,
    53
  ],
  "backdrop_path": "/5qxePyMYDisLe8rJiBYX8HKEyv2.jpg",
  "adult": false,
  "overview": "When an IMF mission ends badly, the world is faced with dire consequences. As Ethan Hunt takes it upon himself to fulfil his original briefing, the CIA begin to question his loyalty and his motives. The IMF team find themselves in a race against time, hunted by assassins while trying to prevent a global catastrophe.",
  "release_date": "2018-07-25"
}
```

Now that you've got this information make a thumbnail that displays the title and the poster. The thumbnail should be an `<a>` tag so it is clickable link to `/movies/:id` where the `:id` is the id provided from the API.

To display images use the `<img src="..."/>` html tag. [Here are the docs for The MovieDB poster url](https://themoviedb.docs.apiary.io/#reference/account/get?console=1).

# Optional Extra Stretch: Genres

Can you also request the [Movie Genres](https://developers.themoviedb.org/3/genres/get-movie-list) from the API, then map the genres into the movie's genres and display them in the thumbnail under the title?
