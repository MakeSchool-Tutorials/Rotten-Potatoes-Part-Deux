---
title: "See One Movie"
slug: see-one-movie
---

Still on the same branch, we're going to keep going and add a show route for the movies.

# Show Route

Now here is a bit of a challenge. Instead of giving you the code, here are some instructions and hints to follow to write the movies show route.

1. Add the new route to `movies.js`
1. Make the route resourceful for the Movie resource show route
1. Use the `monviedb-promise` method `.movieInfo()` and pass to it the movie's MovieDB id from your route. Look at the [method docs here](https://developers.themoviedb.org/3/movies/get-movie-details)
1. Now respond with the `movies-show` template passing the `movie` from the API response.

> [solution]
>
```js
// movies.js

...

app.get('/movies/:id', (req, res) => {
  moviedb.movieInfo({ id: req.params.id }).then(movie => {
    res.render('movies-show', { movie: movie });
  }).catch(console.error)
})


```
>

# Movie Show Template

Now let's make the template. The template should look like this beautiful and masterful wireframe drawing

![movies-show](assets/movies-show.png)

```html
<!-- movies-show.handlebars -->

<div class="row">
    <div class="col-sm-4">
        <img class="img-responsive" src="https://image.tmdb.org/t/p/w500{{movie.poster_path}}"/>
    </div>
    <div class="col-sm-8">
        <h1>{{movie.title}}</h1>
        <p class="lead">{{movie.overview}}</p>
    </div>
</div>

...
```

To display images use the `<img src="..."/>` html tag. [Here are the docs for The MovieDB poster url](https://themoviedb.docs.apiary.io/#reference/account/get?console=1).

# Displaying the Trailer

So what if we want to show the movie's trailer (if it exists)?

Well we can!

1. First we have to query the `/videos` method of the API.
1. Then we can select the first one.
1. Then we can embed the video in the template.

```js
// movies.js

app.get('/movies/:id', (req, res) => {
  moviedb.movieInfo({ id: req.params.id }).then(movie => {
    if (movie.video) {
      moviedb.movieVideos({ id: req.params.id }).then(videos => {
        movie.trailer_youtube_id = videos.results[0].key
        renderTemplate(movie)
      })
    } else {
      renderTemplate(movie)
    }

    function renderTemplate(movie)  {
      res.render('movies-show', { movie: movie });
    }

  }).catch(console.error)
})
```

Now to the template we add a youtube embed statement

```html
<div class="embed-responsive embed-responsive-21by9">
  <iframe class="embed-responsive-item" src="https://www.youtube.com/embed/{{movie.trailer_youtube_id}}?rel=0"></iframe>
</div>
```

# Git Commit

Commit your code!
