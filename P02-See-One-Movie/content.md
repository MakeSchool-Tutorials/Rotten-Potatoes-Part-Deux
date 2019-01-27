---
title: "See One Movie"
slug: see-one-movie
---

Still on the same branch, we're going to keep going and add a `show` route for the movies so those posters will take you somewhere when you click on them!

## User Stories

1. ~~User can see recent movies from TheMovieDB~~
1. **User can see one Movie, including the poster and trailer**
    1. **Build a `/show` route for movies**
    1. **Build a `movies-show` template**
    1. **Add the movie's trailer to the template**
1. User reviews are associated with the corresponding movie
1. Add 100% test coverage for all routes

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

Refresh your browser and make sure the movies have a poster, title, and overview/synopsis. Let's bring in that trailer now.

# Displaying the Trailer

Remember that the users really want to see the movie's trailer so they can decide if they want to go see it. Well let's make their wishes and desires come true! Cause who can ask for more than a movie trailer?

1. First we have to query the `/videos` method of the API. Check out the [moviedb-promise](https://www.npmjs.com/package/moviedb-promise) docs to see how to do that.
1. Then we can select the first one, which will be the trailer. Don't believe us? Try out a few movies in the [/videos docs](https://developers.themoviedb.org/3/movies/get-movie-videos) and see for yourself!
1. Then we can embed the video in the template.

Let's get to it!

>[action]
> Update your `/show` route in `/controllers/movies.js` to the following:
>
```js
// movies.js
>
app.get('/movies/:id', (req, res) => {
  moviedb.movieInfo({ id: req.params.id }).then(movie => {
    moviedb.movieTrailers({ id: req.params.id }).then(videos => {
      movie.trailer_youtube_id = videos.youtube[0].source
      console.log('VIDEOS.TRAILER_YOUTUBE_ID', movie.trailer_youtube_id)
>
      res.render('movies-show', { movie: movie });
    }).catch(console.error);
  }).catch(console.error);
});
```

Now to the template we add a youtube embed statement

>[action]
> Add the trailer to the `/views/movie-show.handlebars` template:
>
```html
<div class="col-sm-12">
    <div class="embed-responsive embed-responsive-16by9">
      <iframe class="embed-responsive-item" src="https://www.youtube.com/embed/{{movie.trailer_youtube_id}}?rel=0"></iframe>
    </div>
</div>
```

Refresh and see if you can play that trailer for Transformers 17! Or maybe Fast and Furious 10 is out by now...

# Now Commit

>[action]
>
```bash
$ git add .
$ git commit -m 'Users can see movie pages and trailers'
$ git push
```

Commit your code to your movies feature branch. Don't merge with master just yet, because we still got some work to do on this feature. Onward!
