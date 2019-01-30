---
title: "Adding AJAX to Forms"
slug: add-ajax
---

Ok so now you are live with your big new refactor, but the job of a developer is never ending.

The first complaint people have is that writing comments is "slow and clunky". This is because we've used a vanilla HTML form's `action=""` attribute to submit the `/comments/new` form. This requires the page to reload which feels clunky. Users would be happier if they could just see the comment pop down into the list of comments, instead of reloading the whole page.

You decide to use **asynchronous `AJAX`** requests to avoid clunky page reloads. Not only will these `AJAX` requests make the site feel snappier at times, it will also move some of the architecture of the project from **Server-Side** or **Monolithic** to **Client-Side** or **Service-Based**.

# New Feature Branch

Following the best practice of using feature branches, we're going to create a new feature branch to add `AJAX` functionality with `Axios` to our project.

>[action]
> Add a new `ajax` branch
>
```bash
$ git checkout -b ajax
```

# Smoke Testing the Script

In order to make an `AJAX` request, we are going to have to write JavaScript scripts that your browser will run. It might feel strange at first, but JavaScript was made for the client, and only recently has been used on the server. So even though you feel a lot more confident with it on the server right now, it was originally developed to do what we are about to do: client-side scripting!

Let's start using JavaScript on the client!

To have custom JavaScript scripts we need to host them on our server, but tell the server to serve them to the client. Then we have to link them to out to our HTML views through at the bottom of the page just before the `</body>` tag ends.

>[action]
> First, make a folder called `public` if it doesn't exist already, and add the following code to your `app.js` file with your other middleware:
>
```js
...
app.use(bodyParser.urlencoded({ extended: true }));
>
// This tells your app to allow parsing of JSON, which we'll need when we use Axios later
app.use(bodyParser.json());
>
// This tells your `Express.js` app to serve all client-side assets in its `public` folder, so that is where we'll put our JavaScript scripts.
app.use(express.static('public'));
```
>
> Now make a `public/javascript` directory and add a file called `scripts.js` with a single `alert()` call:
>
```js
// javascript/scripts.js
alert("hello world");
```

Then link it near the bottom of the page tag of your layout:

>[action]
> Add the script near the bottom of the page in `/views/layouts/main.handlebars`
>
```html
<!-- main.handlebars -->
>
  ...
  <!-- Latest compiled and minified JavaScript -->
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
  <script type="text/javascript" src="/javascript/scripts.js"></script>
</body>
...
```

<!-- -->

>[info]
> We're keeping this script at the bottom of the page because we want it to load after all the DOM elements have loaded. We're going to be manipulating partials later, and if this script isn't at the bottom, they won't load before we use them.

Now refresh your browser and see that your alert works.

# Including Axios and Smoke Testing It

As a shortcut, let's include [Axios](https://github.com/axios) in our client using a [Content Delivery Network (CDN)](https://en.wikipedia.org/wiki/Content_delivery_network).

>[action]
> Add `Axios` to the near the bottom of the page in `/views/layouts/main.handlebars`. Make sure it's underneath `scripts.js`:
>
```html
...
    <!-- Latest compiled and minified JavaScript -->
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <script type="text/javascript" src="/javascript/scripts.js"></script>
</body>
...
```

Ok now we can make our first Axios request. For fun we'll call [thecolorapi.com](thecolorapi.com) (an API for colors and their names).

>[action]
> Update `/public/javascript/scripts.js` to the following:
>
```js
// scripts.js
>
// Make a request to the color api
axios.get('http://www.thecolorapi.com/id?hex=24B1E0')
  .then(function (response) {
    // handle success
    alert(response.data.hex.value);
  })
  .catch(function (error) {
    // handle error
    console.log(error);
  });
```

Refresh your browser and make sure that hex value appears in the alert.

Now that we have scripts and `Axios` ready to go, let's first update the `/comments/new` form to send with `AJAX` instead of with the HTML form.

# Updating the Form with an Id

Let's look at `views/partials/comment-form.handlebars` where people can comment.

>[action]
> change the initial `form` tag in `views/partials/comment-form.handlebars` to the following:
>
```HTML
<form action="#" id="new-comment">
```

We'll use the `id` attribute as a **DOM Selector** to select the form, and then we'll attach an **Event Listener** to the **submit** action. So we'll setup some code on a trigger that will fire when a user submits a form. In essence, we'll be highjacking vanilla HTML forms and using them for our own purposes!

![evil laugh](https://media1.tenor.com/images/e7dad789c2acd076ab3de961c0d43b45/tenor.gif?itemid=4133163)

# Writing the Script and an Event Listener

Now let's use `Axios` to submit the form when the `/comments/new` form is submitted. In psuedocode, it would like this:

```js
// scripts.js

// listen for a form submit event
// prevent the default form behavior
// serialize the form data into an object
// use axios to initialize a post request and send in the form data
// wait for the success response from the server
// remove the information from the form
// display the data as a new comment on the page
// handle any errors

```

Let's take this step by step. We're going to do everything in the above psuedocode, which then leaves us ready to implement the request to the server. Be careful to read each line:

>[action]
> Update `/public/javascript/scripts.js` to the following:
>
```js
// scripts.js
>
// Only run this if we find the new-comment element
if (document.getElementById('new-comment')) {
  // listen for a form submit event
  document.getElementById("new-comment").addEventListener("submit", e => {
      // prevent the default form behavior
      e.preventDefault();
  >
      // serialize the form data into an object
      let comment = {};
      const inputs = document.getElementsByClassName('form-control');
      for (var i = 0; i < inputs.length; i++) {
        comment[inputs[i].name] = inputs[i].value;
      }
  >
      // use axios to initialize a post request and send in the form data
  >
      axios.post('/reviews/comments', comment)
        .then(function (response) {
          // wait for the success response from the server
          console.log(response);
          // remove the information from the form
          // display the data as a new comment on the page
        })
        .catch(function (error) {
          console.log(error);
          // handle any errors
          alert('There was a problem saving your comment. Please try again.')
        });
  });
}
```

That may look like a lot of code, but it gets us a pretty cool thing: the comment just pops off the form and onto the top of the review's comments!

# Responding With JSON

So now we're sending the request **Asynchronously** as an **AJAX Request**. We have to update our server so it behaves properly.

Now if we sent this request to the server code as it stands today what would happen?

> [solution]
> The server `/comments/create` route currently tries to redirect to the current review, but that is not going to work if the request is not synchronous and initiated by AJAX instead of HTML.

We need to update the server `/comments/create` route, so that instead of trying to redirect, it should respond. But not by rendering an HTML template, but by responding with JSON. In this case we'll send back something like this:

```JSON
{
  "status": 200,
  "data": {
    "_id": "2034800sdf8a0sf880adf",
    "title": "Great Review",
    "content": "Seriously you really did a great job."
  }
}
```

The client will be able to see that the message was a success (`status = 200`), and get a confirmation that the comment saved (because it has its own `_id` attribute).

We'll then use that to append this comment to the current HTML page without reloading it.

>[action]
> Update the `/comments/create` route in `/controllers/comments.js` to the following:
>
```js
// comments.js
>
// CREATE Comment
app.post('/movies/:movieid/reviews/:reviewId/comments', (req, res) => {
  Comment.create(req.body).then(comment => {
    res.status(200).send({ comment: comment });
  }).catch((err) => {
    res.status(400).send({ err: err })
  })
})
```

This **API Webhook** or **API Endpoint** does not respond by rendering HTML, it **Responds with JSON**. As we continue to learn, we'll move away from architectures that rely on servers rendering HTML and move towards **Client-Side Architectures** that only respond with JSON.

# Appending the Comment

Now that the server is responding with a little chunk of JSON, we can append that to the client.

So we'll need to set an HTML tag that we can put the comments in.

>[action]
> Wrap the `{{#each comments}}` block in a `div` in the `/views/reviews-show.handlebars` template:
>
```HTML
<!-- views/reviews-show.handlebars -->
>
...
>
{{> comment-form}}
>
<div id="comments">
  {{#each comments}}
    {{> comment comment=this movieId=../review.movieId}}
  {{/each}}
</div>
```

Now we'll use the [`insertAdjacentHTML` function](https://developer.mozilla.org/en-US/docs/Web/API/Element/insertAdjacentHTML) and a [template literal](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals), a feature of JavaScript ES6, to add the comment to the top of the stack of comments.

>[action]
> Save the `movieId`, and then Update your `axios.post/` body to the following in `/public/javascript/scripts.js`:
>
```js
// scripts.js
>
...
// We'll need this for re-constructing our delete form
const movieId = comment.movieId;
axios.post('/reviews/comments', comment)
.then(function (response) {
  // wait for the success response from the server
  console.log("response: ", response);
  // remove the information from the form
  document.getElementById('new-comment').reset();
  // display the data as a new comment on the page
  document.getElementById('comments').insertAdjacentHTML('afterbegin',
    `<div class="card">
        <div class="card-block">
            <h4 class="card-title">${response.data.comment.title}</h4>
            <p class="card-text">${response.data.comment.content}</p>
            <p>
                <form method="POST" action="/movies/${movieId}/reviews/${response.data.comment.reviewId}/comments/${response.data.comment._id}?_method=DELETE">
                    <button class="btn btn-link" type="submit">Delete</button>
                </form>
            </p>
        </div>
    </div>`
  );
})
```

Try adding a comment to a review and see if it displays as the first one. Great work!

Buuuut we're not quite done yet. If you refresh the page or navigate back to it, notice your comment is now at the bottom. That's because the comments are currently sorted in **ascending** order: oldest first. We want them in **descending** order: newest first

Luckily our comments are just an array, so let's just reverse it so they always display in descending order!

>[action]
> Reverse the `comments` array in the `/show` route in `/controllers/reviews.js`:
>
```js
// SHOW
...
Comment.find({ reviewId: req.params.id }).then(comments => {
[bold]    comments.reverse(); [/bold]
    // respond with the template with both values
    res.render('reviews-show', { review: review, comments: comments })
})
...
```

<!-- -->

>[challenge]
> Reversing works, but is a somewhat naive implementation. What would happen if Mongoose changes its default sorting order?
>
> See if you can come up with a more sustainable way to always have comments display in descending order.

# Take a Moment to Reflect

That may look like we added a lot of code, and for what? Actually quite a lot! This setup gets us a pretty cool thing: _when we write a comment, it just pops off the form and onto the top of the review's comments._

As you are using the internet the rest of the week, keep an eye out for how many times your pages are reloading, and how often they don't reload at all. That's AJAX!

As you learn more about the web you'll see that modern web servers are almost 100% AJAX and JSON endpoints.

# Now Commit
Make sure to test your code before committing!

Also remember we have to set the upstream branch again:
>[action]
>
```bash
$ git add .
$ git commit -m 'Added AJAX to comment form'
$ git push --set-upstream origin ajax
```
