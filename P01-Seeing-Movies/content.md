---
title: "Seeing Movies"
slug: seeing-movies
---

# New GitHub Feature Branch

First let's make a new feature branch

```bash
$ git checkout -b movies
$ git branch
```

You should see you are in a new branch called `movies`.

# The MovieDB

We're going to use The MovieDB to see recent and popular movies and search for movies.

[Here are the docs](https://developers.themoviedb.org/3/getting-started/introduction)

Head over, sign up, and [get an API key](https://developers.themoviedb.org/3/getting-started/introduction)

In Application URL just put "N/A", and in your Application Summary put "Building a website and using your API only for educational purposes. Thanks!"

# Movies Resource

We are going to create a **Movie** resource, but it won't have a model or a collection, because the movies all live inside The MovieDB, but we will have a `movies.js` controller to house the Movie resource routes. We'll only need two movies routes:


| URL              | HTTP Verb | Action  | What it Does |
|------------------|-----------|---------|---------------|
| /movies          | GET       | index   | See all movies |
| /movies/:id      | GET       | show    | See one movie |

So make that `movies.js` file and add it to your `app.js`.

# Updating Root Route

When you go to Rotten Tomatoes, notice that the root route is not actually of reviews, it is of movies. We shoul do that too!

Let's comment out the `/` route in `reviews.js` and add a root route to our `movies.js` file

```js
app.get('/', (req, res) => {
  res.render('movies-index');
})
```

Now you'll need a `movies-index.handlebars` template with a little

```html
<h1>Hello Movies</h1>
```

# Calling our API

Now let's call the API.

A quick search of [npmjs.org](https://www.npmjs.com/package/moviedb-promise) reveals that there is a wrapper for the MovieDB API. Let's use it to simplify our lives.

```bash
$ npm install moviedb-promise --save
```

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

Play around with a sample request with their awesome [API method docs and playground](https://developers.themoviedb.org/3/movies/get-now-playing). Make sure to put in your API key into the queries.

We used the `miscNowPlayingMovies()` method, but you can use any you find in the [moviedb-promise docs](https://www.npmjs.com/package/moviedb-promise) and the [Movies section of the MovieDB API docs](https://developers.themoviedb.org/3/movies/get-movie-details).

And now we need to show the list of results in the template.

```html
<h1>Now Playing</h1>
{{#each movies}}
  <li>{{this.title}}</li>
{{/each}}
```

Here's what we have to play with:

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

Now that you've got this information make a thumbnail that displays the title and the poster. The thumbnail should be an <a> tag so it is clickable and go to `/movies/:id` where the `:id` is the id provided from the API.

Hint: to show an image with the poster use something like this: `<img src="{{this.poster_path}}" />`.

# Optional Extra Stretch: Genres

Can you also request the [Movie Genres](https://developers.themoviedb.org/3/genres/get-movie-list) from the API, then map the genres into the movie's genres and display them in the thumbnail under the title?
