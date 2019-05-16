---
title: "Deleting Comments with AJAX"
slug: deleting-comments-with-ajax
---

## User Stories

1. ~~User can see recent movies from TheMovieDB~~
1. ~~User can see one Movie, including the poster and trailer~~
1. ~~User reviews are associated with the corresponding movie~~
1. **Users can add or delete comments without requiring a page refresh**
    1. ~~Add `AJAX` and `Axios` to the app~~
    1. ~~Create client-side JavaScript to handle events and call `Axios`~~
    1. ~~Update our views and controllers to handle JSON responses from `Axios`~~
    1. ~~Use `Axios` to POST Comments (handle the `/create` route)~~
    1. **Use `Axios` to DELETE Comments (handle the `/delete` route)**
1. Users can access an admin dashboard that lists all reviews and allows them to delete them

We can create comments asynchronously now using a **Client-Side Architecture**, but that's only half the story for comments. Let's also remove them async. They'll just disappear without a page reload.

# Updating the Template

Since we are going to send a delete message straight to the server with AJAX, we don't actually even need the form tag we were using before. We can just use a button. But we need to include the comment's id otherwise we won't know which comment to delete, so we can use a `data-` HTML attribute and customize it to be called `data-comment-id`.

>[action]
> Replace the `<p><form>` element in `/views/partials/comment.handlebars` to this  `button` element:
>
```html
<!-- views/partials/comment.handlebars -->
>
...
<button class="btn btn-link delete-comment" data-comment-id="{{comment._id}}" data-comment-reviewId="{{comment.reviewId}}" data-comment-movieId="{{movieId}}">Delete</button>
```
> Also change the `<p><form>` element within the template in `/public/javascript/scripts.js` to the this `button` element Don't forget the double quotes `"`:
>
```js
> // scripts.js
...
document.getElementById('comments').insertAdjacentHTML('afterbegin',
  `<div class="card">
     <div class="card-block">
       <h4 class="card-title">${response.title}</h4>
       <p class="card-text">${response.content}</p>
       <button class="btn btn-link delete-comment" data-comment-id="${response.data.comment._id}" data-comment-reviewId="${response.data.comment.reviewId}" data-comment-movieId="${movieId}"">Delete</button>
     </div>
   </div>`
);
...
```


# Add our Script Using Axios

Now we need to detect the click event on the `commentId` button and submit an `axios.delete()` function.

>[action]
> Add the following after the code for `new-comment` in `/public/javascript/scripts.js`:
>
```js
// scripts.js
>
if (document.getElementById('new-comment')) {
...
}
>
// Check to make sure we see any .delete-comment elements
if (document.querySelectorAll('.delete-comment')) {
    // Add a click event listener for each comment
    document.querySelectorAll('.delete-comment').forEach((commentElement) => {
        commentElement.addEventListener('click', (e) => {
            console.log("click!")
            // Store all of the attributes into variables
            let commentId = e.target.getAttribute('data-comment-id')
            let reviewId = e.target.getAttribute('data-comment-reviewId')
            let movieId = e.target.getAttribute('data-comment-movieId')
            axios.delete(`/movies/${movieId}/reviews/${reviewId}/comments/${commentId}`)
                .then(response => {
                    console.log(response)
                })
                .catch(error => {
                    console.log(error)
                    alert('There was an error deleting this comment.')
                });
        })
    })
}
```

Client side is now partially completed (we'll revisit this shortly to fully finish it). Now we can focus on the server side.

# Updating the Server to Respond to JSON and the Script Response

Now we need to update the server to respond with JSON so we can manage the whole interaction with the client code.

>[action]
> Update the `/delete` route in `/controllers/comments.js` to respond with JSON:
>
```js
// comments.js
>
...
>
// DELETE
app.delete('/movies/:movieId/reviews/:reviewId/comments/:id', (req, res) => {
  console.log("DELETE comment")
  Comment.findByIdAndRemove(req.params.id).then(comment => {
    res.status(200).send(comment);
  }).catch((err) => {
    console.log(err.message);
    res.status(400).send(err)
  })
})
```

Now the server is sending back JSON after deleting the comment. Let's update the script to remove the deleted comment from the DOM.

In order to accomplish this we need each comment's `_id` attribute to be attached to it's enclosing `div` tag.

> [action]
> Update `/views/partials/comment.handlebars` to give the outermost `div` and `id` attribute:
>
```HTML
<!-- views/partials/comment.handlebars -->
>
<div class="card" id="{{comment._id}}">
  ...
</div>
```
>
> Make the same update in the `new-comment` template within `/public/javascript/scripts.js`:
>
```js
...
>
document.getElementById('comments').insertAdjacentHTML('afterbegin',
    `<div class="card" id="${response.data.comment._id}">
       <div class="card-block">
         <h4 class="card-title">${response.title}</h4>
         <p class="card-text">${response.content}</p>
         <button class="btn btn-link delete-comment" data-comment-id="${response.data.comment._id}" data-comment-reviewId="${response.data.comment.reviewId}" data-comment-movieId="${movieId}">Delete</button>
       </div>
     </div>`
);
...
```

Now that we've got the `id` we can remove it from the view:

>[action]
> Update the `axios.delete` response to remove the comment in `/publc/javascript/scripts.js`
>
```js
// scripts.js
...
axios.delete(`/movies/${movieId}/reviews/${reviewId}/comments/${commentId}`)
    .then(response => {
        console.log(response)
        comment = document.getElementById(commentId)
        comment.parentNode.removeChild(comment); // OR comment.style.display = 'none';
    })
    .catch(error => {
        console.log(error)
        alert('There was an error deleting this comment.')
    });
```

Try deleting one of your existing comments. Make sure it works properly before moving on!

# Deletion Refactor

You may have noticed the delete functionality isn't quite 100% yet. Try creating a new comment and then immediately deleting it. It doesn't delete!

**Why do you think this is happening?**

>[solution]
>
> `scripts.js` only runs on when a page loads. Since we're no longer refreshing the page to add a comment, the scripts don't run, and the new comment doesn't get the delete click event added to its delete button!

What we want to do is take our delete code in `scripts.js` and run it immediately after we insert the new comment onto the page. Since we're going to be re-using the same code for more than one purpose, let's make a function for it!

>[action]
> At the top of `/public/javascript/scripts.js`, add a new function called `deleteComment()` which contains the same code we currently use to add click handlers to delete comments:
>
```js
// scripts.js
>
function deleteComment() {
    document.querySelectorAll('.delete-comment').forEach((commentElement) => {
        commentElement.addEventListener('click', (e) => {
            console.log("click!")
            let commentId = e.target.getAttribute('data-comment-id')
            let reviewId = e.target.getAttribute('data-comment-reviewId')
            let movieId = e.target.getAttribute('data-comment-movieId')
            // Same code used to add click handlers, except we call delete here
            axios.delete(`/movies/${movieId}/reviews/${reviewId}/comments/${commentId}`)
                .then(response => {
                    console.log(response)
                    comment = document.getElementById(commentId)
                    comment.parentNode.removeChild(comment); // OR comment.style.display = 'none';
                })
                .catch(error => {
                    console.log(error)
                    alert('There was an error deleting this comment.')
                });
        })
    })
}
```

Now we have this function we can use in multiple places! Let's call it after we add a new comment, and also when we load a page (i.e. in the same location the code was before).

> [action]
> Call the `deleteComment` method in the following places within `/public/javascript/scripts.js`, removing the duplicated code from the `document.querySelectorAll('.delete-comment')` area:
>
```js
// scripts.js
...
>
// listen for a form submit event
document.getElementById('new-comment').addEventListener("submit", e => {
>
...
>
  document.getElementById('comments').insertAdjacentHTML('afterbegin',
    ...
  );
  >
  // Call this after we use insertAdjacentHTML to add a new comment
  deleteComment();
  >
})
>
// Still need to call deleteComment for all other comments on the page when we load a review
if (document.querySelectorAll('.delete-comment')) {
    deleteComment();
}
```

Now try to create a new comment and immediately delete it. Now we can instantly delete those mistakes!

# Now Commit
Make sure to test your code before committing!

>[action]
>
```bash
$ git add .
$ git commit -m 'Deleting comments with AJAX'
$ git push
```

# Now Merge

Now merge this branch with master, and push to github and heroku.

>[action]
> Merge this branch into master
>
```bash
$ git checkout master
$ git merge ajax
```
>
> Now manually retest your code in master to see that everything is working well.

# Now Push

Now push to your remote github repo and Heroku.

> [action]
> Push to master and Heroku:
>
```bash
$ git push origin master
$ git push heroku master
```

You got POST down, and now you've successfully **used `AJAX` requests via `Axios` to do DELETE actions to the server!** You also got more practice **creating client-side scripts using JavaScript, and using a Feature Branch strategy**. One more User Story to go!

# Feedback and Review - 2 minutes

**We promise this won't take longer than 2 minutes!**

Please take a moment to rate your understanding of learning outcomes from this tutorial, and how we can improve it via our [tutorial feedback form](https://goo.gl/forms/jAxU2lxXIWHh4AIq1)

This allows us to get feedback on how well the students are grasping the learning outcomes, and tells us where we can improve the tutorial experience.
