---
title: "Styling with Bootstrap"
slug: adding-bootstrap
---

Now that you have the user's experience mapped out and the functionality built, you can style your pages using Twitter's project [Bootstrap](http://getbootstrap.com/). Bootstrap is the web's most popular **CSS Framework**.

The main elements we will be using are the...

* Grid
* Navbar
* Forms
* Inputs
* Buttons

There is a lot more that you can explore and even extend and modify.

# Adding Bootstrap with a Content Delivery Network (CDN)

It is easy to get started with bootstrap quickly by using the CDN links bootstrap maintains.

We'll add the `<link>` to bootstrap's css in our `<head>` tag in the `main.handlebars` file. And although we aren't going to use any of bootstrap's JavaScript's components, for completeness's sake, we'll put the JavaScript `<script>` tag just before the closing `</body>` tag.

```html
<!-- views/layouts/main.handlebars -->

<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>Rotten Potatoes</title>
  <!-- Latest compiled and minified CSS -->
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
</head>
<body>

  {{{body}}}

  <!-- Latest compiled and minified JavaScript -->
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
</body>
</html>
```

Just after doing that, refresh your page. The typography and layout should change just a tiny bit if it worked.

# Adding a Navbar

Add the most common navigational component - a top navbar. We'll have it have a button to create a new review so that anywhere a user navigates to they can always make a new review.

Make a new partial. Create a new file `navbar.handlebars` in the `views/partials` directory.

```html
<!-- views/partials/navbar.handlebars -->

<nav class="navbar navbar-default">
  <div class="container-fluid">
    <div class="navbar-header">
      <a class="navbar-brand" href="/">
        Rotten Potatoes
      </a>
    </div>
    <div class="pull-right">
      <a href="/reviews/new" class="btn btn-default navbar-btn">New Review</a>
    </div>
  </div>
</nav>
```

Include this in `views/layouts/main.handlebars`

```html
...
<body>

  {{> navbar}}

  {{{body}}}
 ...
```

# Adding a Grid Container

People argue about the usefulness of some of the more complex parts of Bootstrap, but everyone agrees that to build websites you need a grid system. Bootstrap ships with a 12-column grid. Meaning you can break up the whole page, or any element into 12 columns and that way construct an appealing layout.

We'll start by wrapping the `{{{body}}}` with a `container` class.

```html
<!-- views/layouts/main.handlebars -->
...
<body>
  ...
  <div class="container">
    {{{body}}}
  </div>
...
```

Refresh and see that this gives us some satisfying gutters on the sides of the page. If we shringchange the screensize by resizing the window, we'll see that these gutters vanish when our screen shrinks beyond a certain size. This is because Bootstrap's grid system is **Responsive** meaning it changes depending on the size of the screen that is being shown on. Pretty neat!

# Adding a Grid Row

Now that you have a container, style your `reviews-index` template to have each review take up 1/4 of the `container`. First  wrap the `{{#each}}` block in a `row` class and then wrap each review in its own `col-sm-3` class.

`col-sm-3` means: On screens larger than a tablet, have this element take up three cells out of twelve that are the whole width of its parent element. Since 3/12 = 1/4 this will give us each review taking up one quarter of the `container`'s width.

```html
<!-- views/reviews-index.handlebars -->

<h1>Reviews</h1>

<div class="row">
  {{#each reviews}}
    <div class="col-sm-3">
      <h2><a href="/reviews/{{this._id}}">{{this.title}}</a></h2>
      <small>{{this.movieTitle}}</small>
    </div>
  {{/each}}
</div>
```

On to the `review-show` template

# Styling the Show Template

The show template should make the text relatively narrow, because people don't like reading all the way across the screen. Use an `offset` grid class to push a column over to the right.

```html
<!-- views/reviews-show.handlebars -->

<a href="/">Back to Home</a>
<div class="row">
  <div class="col-sm-6 col-sm-offset-3">
    <h1>{{review.title}}</h1>
    <h2>{{review.movieTitle}}</h2>
    <p>{{review.description}}</p>
    <p><a href="/reviews/{{review._id}}/edit">Edit</a></p>
    <p><form method="POST" action="/reviews/{{review._id}}?_method=DELETE">
        <button class="btn btn-primary" type="submit">Delete</button>
    </form></p>
  </div>
</div>
```

# Styling the Forms

Now let's add some bootstrap styling to our new and edit templates and to our `reviews-form` template.

```html
<!-- views/reviews-new.handlebars -->

<div class="row">
  <div class="col-sm-6 col-sm-offset-3">
    <form method="POST" action="/reviews">
      {{> reviews-form}}
      <!-- BUTTON -->
      <div class="form-group">
        <button class="btn btn-primary" type="submit">Save Review</button>
      </div>
    </form>
  </div>
</div>
```

Make the same changes to `reviews-edit.handlebars`:

```html
<!-- views/reviews-edit.handlebars -->

<div class="row">
  <div class="col-sm-6 col-sm-offset-3">
    <form method="POST" action="/reviews/{{review._id}}?_method=PUT">
      {{> reviews-form}}
      <!-- BUTTON -->
      <div class="form-group">
        <button class="btn btn-primary" type="submit">Save Review</button>
      </div>
    </form>
  </div>
</div>

```

Now add some Bootstrap classes to `reviews-form.handlebars` partial. These class will make your form and form elements look better. Notice that each for element is wrapped in a `div` with the class `form-group` and form elements, like `input` get the class `form-control`.

```html
...
<fieldset>
  <legend>Edit Review</legend>
  <!-- TITLE -->
  <div class="form-group">
    <label for="review-title">Title</label><br>
    <input class="form-control" id="review-title" type="text" name="title" value="{{review.title}}"/>
  </div>

  <!-- MOVIE TITLE -->
  <div class="form-group">
    <label for="movie-title">Movie Title</label><br>
    <input class="form-control" id="movie-title" type="text" name="movieTitle" value="{{review.movieTitle}}" />
  </div>

  <!-- DESCRIPTION -->
  <div class="form-group">
    <label for="review-description">Description</label><br>
    <textarea class="form-control" id="review-description" name="description" rows="10" />{{review.description}}</textarea>
  </div>
</fieldset>
```

How does she look?

# Now Commit

```bash
$ git add .
$ git commit -m 'Added vanilla bootstrap'
$ git push
```

# Onward! Almost Done!

Almost done! Let's finish it off.
