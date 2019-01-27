---
title: "See One Movie"
slug: see-one-movie
---

Still on the same branch, we're going to keep going and add a `show` route for the movies so those posters will take you somewhere when you click on them!

# Show Route

Now here is a bit of a challenge. Instead of giving you the code, here are some instructions and hints to follow to write the movies `show` route.

1. Add the new route to `movies.js` controller.
1. Make the route resourceful for the Movie resource `show` route
1. Use the `monviedb-promise` method `.movieInfo()` and pass to it the movie's MovieDB id from your route. Look at the [method docs here](https://developers.themoviedb.org/3/movies/get-movie-details)
1. Now respond with the `movies-show` template passing the `movie` from the API response. _Don't worry about making a perfect template right now,_ we'll build it out in the next section. Just make it go to something with an `h1` tag for now.

> [solution]
>
> ```js
> // movies.js
>
> ...
>
> app.get('/movies/:id', (req, res) => {
>   moviedb.movieInfo({ id: req.params.id }).then(movie => {
>     res.render('movies-show', { movie: movie });
>   }).catch(console.error)
> })
> ```
>

# Movie Show Template

Now let's make the template. The template should look like this beautiful and masterful wireframe drawing

![movies-show](assets/movies-show.png)

>[action]
> Use the following code for your `/views/movies-show.handlebars` template:
>
```html
<!-- movies-show.handlebars -->
>
<div class="row">
    <div class="col-sm-4">
        <img class="img-responsive" src="https://image.tmdb.org/t/p/w300{{movie.poster_path}}"/>
    </div>
    <div class="col-sm-8">
        <h1>{{movie.title}}</h1>
        <p class="lead">{{movie.overview}}</p>
    </div>
</div>
```

Remember, we're constructing the URL from the [docs from the last chapter](https://developers.themoviedb.org/3/configuration/get-api-configuration).

Got our poster, title, and overview/synopsis. Let's bring in that trailer now.

# Displaying the Trailer

Remember that the users really want to see the movie's trailer so they can decide if they want to go see it. Well let's make their wishes and desires come true! Cause who can ask for more than a movie trailer?

1. First we have to query the `/videos` method of the API.
1. Then we can select the first one.
1. Then we can embed the video in the template.

```js
// movies.js

router.get('/movies/:id', (req, res) => {
  moviedb.movieInfo({ id: req.params.id }).then(movie => {
    moviedb.movieTrailers({ id: req.params.id }).then(videos => {
      movie.trailer_youtube_id = videos.youtube[0].source
      console.log('VIDEOS.TRAILER_YOUTUBE_ID', videos.trailer_youtube_id)

      res.render('movies-show', { movie: movie });
    }).catch(console.error);
  }).catch(console.error);
});
```

Now to the template we add a youtube embed statement

```html
<div class="col-sm-12">
    <div class="embed-responsive embed-responsive-16by9">
      <iframe class="embed-responsive-item" src="https://www.youtube.com/embed/{{movie.trailer_youtube_id}}?rel=0"></iframe>
    </div>
  </div>
```

# Git Commit

Commit your code to your movies feature branch. Don't merge with master yet, because we are not done. Onward!
