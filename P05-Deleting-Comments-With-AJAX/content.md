---
title: "Deleting Comments with AJAX"
slug: deleting-comments-with-ajax
---

We can create comments asynchronously now using a **Client-Side Architecture**, but that's only half the story for comments. Let's also remove them async.

# Updating the Template

Since we are going to send a delete message straight to the server with AJAX, we don't actaully even need the form tag we were using before. We can just use a button. But we need to include the comment's id otherwise we won't know which comment to delete, so we can use a `data-` HTML attribute and customize it to be called `data-comment-id`.

Change this:

```HTML
<form method="POST" action="/reviews/comments/${response._id}?_method=DELETE">
  <button class="btn btn-link" type="submit">Delete</button>
</form>

```

to this in both your `scripts.js` and your `comment.handlebars` files.

```html
<button class="btn btn-link" id="deleteComment" data-comment-id=${response._id}>Delete</button>

<button class="btn btn-link" id="deleteComment" data-comment-id="{{this._id}}">Delete</button>
```

# Add our Script Using Axios

No we need to detect the click event on the `commentId` button and submit an `axios.delete()` function.

```js
document.getElementById('deleteComment').addEventListener('click', (e) => {
  console.log("click!")
  let commentId = this.getAttribute('data-comment-id')
  axios.delete(`/reviews/comments/${commentId}`)
    .then(response => {
      console.log(response)
    })
    .catch(error => {
      console.log(error)
    });
})
```

Now we can see if the response is working by clicking the button both by opening our browser's console and seeing "click!" printed out, and by refreshing the page and see if the comment we clicked on is deleted.

# Updating the Server to Respond to JSON and the Script Response

Now we need to update the server to respond with JSON so we can manage the whole interaction with the client code.

```js
// comments.js

...

// DELETE
app.delete('/reviews/comments/:id', function (req, res) {
  console.log("DELETE comment")
  Comment.findByIdAndRemove(req.params.id).then(comment => {
    res.status(200).send(comment);
  }).catch((err) => {
    console.log(err.message);
    res.status(400).send(err)
  })
})
```

Now the server is sending back JSON after deleting the comment. Now let's update the script to remove the deleted comment from the DOM.

In order to accomplish this we need each comment's `_id` attribute to be attached to it's enclosing division tag.

```HTML
<!-- comment.handlebars -->

<div class="card" id="{{this._id}}">
  <div class="card-block">
    <h4 class="card-title">{{this.title}}</h4>
    <p class="card-text">{{this.content}}</p>
    <!-- Delete link -->
    <p><button class="btn btn-link" id="deleteComment" data-comment-id=${response._id}>Delete</button></p>
  </div>
</div>
```

And we need to update this in the `scripts.js` file

```js
`
 <div class="card" id="${this._id}">
   <div class="card-block">
     <h4 class="card-title">${response.title}</h4>
     <p class="card-text">${response.content}</p>
     <p><button class="btn btn-link" id="deleteComment" data-comment-id=${response._id}>Delete</button></p>
   </div>
 </div>
`
```

Now that we've got the id we can remove it:

```js
document.getElementById('deleteComment').addEventListener('click', (e) => {
  console.log("click!")
  let commentId = this.getAttribute('data-comment-id')
  axios.delete(`/reviews/comments/${commentId}`)
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
```

# Git Commit

Test your code and commit it to the current branch (ajax).

Now merge this branch with master, and push to github and heroku.
