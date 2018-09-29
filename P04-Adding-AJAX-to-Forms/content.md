---
title: "Adding AJAX to Forms"
slug: add-ajax
---

Ok so now you are live with your big new refactor, but the job of a developer is never ending.

The first complaint people have is that writing comments is "slow and clunky". This is because we've used a vanilla HTML form's `action=""` attribute to submit the comments#new form. This requires the page to reload which feels clunky. Users would be happier if they could just see the comment pop down into the list of comments, instead of reloading the whole page.

You decide to use asynchronous AJAX requests to avoid clunky page reloads. Not only will these AJAX requests make the site feel snappier at times, it will also move some of the architecture of the project from a **Server-Side** or **Monolithic** to a **Client-Side** or **Service-Based** architecture.

# New Feature Branch

Following the best practice of using feature branches, we're going to create a new feature branch to add AJAX functionality with Axios to our project.

```bash
$ git checkout -b ajax
$ git branch
```

# Smoke Testing the Script

In order to make AJAX request, we are going to have to write JavaScript scripts that your browser will run. It might feel strange at first, but JavaScript was made for the client, and only recently has been used on the server. So even though you feel a lot more confident with it on the server right now, it was originally developed to do what we are about to do: client-side scripting!

Let's start using JavaScript on the client (what it was invented for!).

To have custom JavaScript scripts we need to host them on our server, but tell the server to serve them to the client. Then we have to link them to out HTML in our `<head></head>` tag or at the bottom of the page just before the `</body>` tag ends.

First, make a folder called `public`, and add the following code to your `app.js` file with your other middleware. This code will tell your Express.js app to serve all client-side assets in its `public` folder, so that is where we'll put our JavaScript scripts.

```js
app.use(express.static('public'));
```

Now make a `public/javascript` directory and add a file called `scripts.js` with a single `alert()` call:

```js
// javascript/scripts.js
alert("hello world");
```

Then link it in the `<head>` tag of your layout like this.

```html
<!-- main.handlebars -->

<head>
  ...
  <script type="text/javascript" src="/javascript/scripts.js"></script>
</head>
```

Now check and see that your alert works.

# Including Axios and Smoke Testing It

As a shortcut, let's include [Axios](https://github.com/axios) in our client using a Content Delivery Network (CDN).


```html
<head>
  ...
  <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
  <script type="text/javascript" src="/javascript/scripts.js"></script>
  ...
</head>
```

Ok now we can make our first Axios request. For fun we'll call thecolorapi.com (an API for colors and their names).

```js
// scripts.js

// Make a request to the color api
axios.get('http://www.thecolorapi.com/id?hex=24B1E0')
  .then(function (response) {
    // handle success
    alert(response.hex.value);
  })
  .catch(function (error) {
    // handle error
    console.log(error);
  });

```

Now that we have scripts and Axios ready to go, let's first update the comments#new form to send using AJAX instead of with the HTML form.

# Updating the Form with an Id

Let's look at `reviews-show.handlebars` where people can comment. We'll change the action from

```HTML
<form action="/reviews/comments" method="post">
```

to

```HTML
<form action="#" id="new-comment">
```

We'll use the `id` attribute as a **DOM Selector** to select the form, and then we'll attach an **Event Listener** to the **submit** action. So we'll setup some code on a trigger that will fire when a user submits a form. In essence, we'll be highjacking vanilla HTML forms and using them for our own purposes!!! hahahahhahha!!! (maniacal laughter).

# Writing the Script and an Event Listener

Now let's use Axios to submit the form instead when the comments#new form is submitted. In psuedocode that is like this:

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

Let's take this step by step. We're going to do everything upto the request to the server (because we have to go build that!). Be careful to read each line:

```js
// scripts.js

// listen for a form submit event
document.getElementById("new-comment").addEventListener("submit", e => {
    // prevent the default form behavior
    e.preventDefault();

    // serialize the form data into an object
    let comment = {};
    const inputs = document.getElementsByClassName('form-control');
    for (var i = 0; i < inputs.length; i++) {
      comment[inputs[i].name] = inputs[i].value;
    }

    // use axios to initialize a post request and send in the form data

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
```

That may look like a lot of code, but it gets us a pretty cool thing, which is the comment just pops off the form and onto the top of the review's comments.

# Responding With JSON

So now we're sending the request **Asynchronously** as an **AJAX Request**. We have to update our server so it behaves properly.

Now if we sent this request to the server code as it stands today what would happen?

> [solution]
> The server comments#create route currently tries to redirect to the current review, but that is not going to work if the request is not synchronous and initiated by AJAX instead of HTML.

We need to update the server comments#create route, so that instead of trying to redirect, it should respond, but not by rendering an HTML template, but by responding with JSON. In this case we'll send back this:

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

The client will be able to see that the message was a success (status = 200), and get a confirmation that the comment saved (because it has its own `_id` attribute).

We'll then use that to append this comment to the current HTML page without reloading it.

```js
// comments.js

// CREATE Comment
app.post('/reviews/comments', (req, res) => {
  Comment.create(req.body).then(comment => {
    res.status(200).send({ comment: comment });
  }).catch((err) => {
    res.status(400).send({ err: err })
  })
})
```

This **API Webhook** or **API Endpoint** does not respond by rendering HTML it **Responds with JSON**. As we continue to learn, we'll move away from architectures that rely on servers rendering HTML and move towards **Client-Side Architectures** that only respond with JSON.

# Appending the Comment

Now that the server is responding with a little chunk of JSON we can append that to the client.

So we'll need to set an HTML tag that we can put the comments in.

```HTML
<!-- views/reviews-show.handlebars -->

...

{{> comment-form}}

<div id="comments">
  {{#each comments}}
    {{> comment}}
  {{/each}}
</div>
```

Now we'll use the `.prepend()` function and a [template literal](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals), a feature of JavaScript ES6, to add the comment to the top of the stack of comments.

```js
// scripts.js

...
    axios.post('/user', comment)
      .then(function (response) {
        // wait for the success response from the server
        console.log(response);
        // remove the information from the form
        this.reset();
        // display the data as a new comment on the page
        document.getElementById('comments').prepend(
          `
           <div class="card">
             <div class="card-block">
               <h4 class="card-title">${response.title}</h4>
               <p class="card-text">${response.content}</p>
               <p>
                  <form method="POST" action="/reviews/comments/${response._id}?_method=DELETE">
                    <button class="btn btn-link" type="submit">Delete</button>
                  </form>
               </p>
             </div>
           </div>
          `
        );
      })
```

# Wha?

That may look like we added a lot of code, and for what? Actually quite a lot! This setup gets us a pretty cool thing. When we write a comment, it just pops off the form and onto the top of the review's comments.

As you are using the internet the rest of the week, keep an eye out for how many times your pages are reloading, and how often they don't reload at all. That's AJAX!

As you learn more about the web you'll see that modern web servers are almost 100% AJAX and JSON endpoints.

# Git Commit

Test your code and commit it to the current branch (ajax).
