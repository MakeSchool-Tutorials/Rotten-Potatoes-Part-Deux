---
title: "Styling with Bootstrap"
slug: adding-bootstrap
---

So now that we have the user's experience mapped out and the functionality built, we can style our pages using Twitter's project [Bootstrap](http://getbootstrap.com/). Bootstrap is the web's most popular CSS framework.

The main elements we will be using are the...

* Grid
* Navbar
* Forms
* Buttons

But there is a lot more that you can explore and even extend and modify.

# Adding Bootstrap with a Content Delivery Network (CDN)

Although not stable enough for production, it is easy to get started with bootstrap quickly by using the CDN links bootstrap maintains.

We'll add the `<link>` to bootstrap's css in our `<head>` tag in the `main.handlebars` file. And although we aren't going to use any of bootstrap's JavaScript's components, for completeness's sake, we'll put the JavaScript `<script>` tag just before the closing `</body>` tag.

```html
<!-- main.handlebars -->
<!DOCTYPE html>
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

Let's add the most common navigational component - a top navbar. We'll have it have a button to create a new review so that anywhere a user navigates to they can always make a new review.

```html
<!-- main.handlebars -->
...
<body>

  <nav class="navbar navbar-default">
    <div class="container-fluid">
      <div class="navbar-header">
        <a class="navbar-brand" href="#">
          Rotten Potatoes
        </a>
      </div>
      <div class="pull-right">
        <a href="/reviews/new" class="btn btn-default navbar-btn">New Review</a>
      </div>
    </div>
  </nav>

  {{{body}}}

  <!-- Latest compiled and minified JavaScript -->
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
</body>
```

# Adding a Grid Container

People argue about the usefulness of some of the more complex parts of Bootstrap, but everyone agrees that to build websites you need a grid system. Bootstrap ships with a 12-column grid. Meaning you can break up the whole page, or any element into 12 columns and that way construct an appealing layout.

We'll start by wrapping the `{{{body}}}` with a `container` class.

```html
<!-- main.handlebars -->
...
<body>
  <div class="container">
    {{{body}}}
  </div>

  <!-- Latest compiled and minified JavaScript -->
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
</body>
```

Refresh and see that this gives us some satisfying gutters on the sides of the page. If we shring the screensize by resizing the window, we'll see that these gutters vanish when our screen shrinks beyond a certain size. This is because Bootstrap's grid system is **Responsive** meaning it changes depending on the size of the screen that is being shown on. Pretty neat!

# Adding a Grid Row

Now that we have a container, let's style our `reviews-index` template to have each review take up 1/4 of the `container`. First we wrap the `{{#each}}` block in a `row` class and then each review we wrap in its own `col-sm-3` class. `col-sm-3` means: On screens larger than a tablet, have this element take up three cells out of twelve that are the whole width of its parent element. Since 3/12 = 1/4 this will give us each review taking up one quarter of the `container`'s width.

```html
<h1>Reviews</h1>
<div class="row">
  {{#each reviews}}
    <div class="col-sm-3">
      <a href="/reviews/{{this._id}}">
        <h5>{{this.title}}</h5>
        <small>{{this.movieTitle}}</small>
        <p>{{this.description}}</p>
      </a>
      <a href="/reviews/{{this._id}}/edit">Edit</a>
      <form method="POST" action="/reviews/{{this._id}}?_method='DELETE'">
        <button type="submit">Delete</button>
      </form>
    </div>
  {{/each}}
</div>
```

On to the `review-show` template

# Styling the Show Template

The show template we should make the text relatively narrow, because people don't like reading all the way across the screen. We can use an `offset` grid class to push a column over to the right.

```html
<!-- views/reviews-show.handlebars -->

<a href="/">Back to Home</a>
<div class="row">
  <div class="col-sm-6 col-sm-offset-3">
    <h1>{{review.title}}</h1>
    <h5>{{review.movieTitle}}</h5>
    <p>{{review.description}}</p>
  </div>
</div>

```

# Styling the Forms

Now let's add some bootstrap styling to our new and edit templates and to our `reviews-form` template.

```html
<!-- reviews-new.handlebars -->

<div class="row">
  <div class="col-sm-6 col-sm-offset-3">
    <legend>New Review</legend>
    <form method="POST" action="/reviews">
      {{> partials/reviews-form }}
    </form>
  </div>
</div>

```

```html
<!-- reviews-edit.handlebars -->

<div class="row">
  <div class="col-sm-6 col-sm-offset-3">
    <legend>Edit Review</legend>
    <form method="POST" action="/reviews?_method=PUT">
      {{> partials/reviews-form }}
    </form>
  </div>
</div>
```

```html
<!-- views/partials/reviews-form.handlebars -->

<!-- TITLE -->
<div class="form-group">
  <label for="title">Title</label>
  <input class="form-control" type="text" name="title" value="{{review.title}}"/>
</div>

<!-- MOVIE TITLE -->
<div class="form-group">
  <label for="movieTitle">Movie Title</label>
  <input class="form-control" type="text" name="movieTitle" value="{{review.movieTitle}}" />
</div>

<!-- DESCRIPTION -->  
<div class="form-group">
  <label for="description">Description</label>
  <textarea class="form-control" name="description" rows="10" />{{review.description}}</textarea>
</div>
<!-- BUTTON -->
<button type="submit" class="btn btn-primary">Save Review</button>
```

How does she look?

# Onward! Almost Done!

Almost done! Let's finish it off.
