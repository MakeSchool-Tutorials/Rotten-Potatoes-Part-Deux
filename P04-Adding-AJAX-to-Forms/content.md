---
title: "Adding AJAX to Forms"
slug: add-ajax
---



# New Feature Branch

Following the best practice of using feature branches, we're going to create a new feature branch to add AJAX functionality with Axios to our project.

```bash
$ git checkout -b ajax
$ git branch
```

# Fetch vs. Axios

Why are we using Axios if it is just a wrapper for JavaScript's ES6 function `fetch`? Why not just use fetch by itself.

Let's look at the pro's and con's

Cons
* Having to add Axios anywhere we will use it on the client
* Having to learn Axios' API
* Having to conform to any of Axios' idiosyncrasies
* Needlessly going to a higher level of abstraction

Pros
* Axios parses JSON responses automatically meaning fewer steps and less code
* Axios has a purely resouceful API, so if you know resouceful development you already know its API
* Mirroring resourceful syntax on the client and the server
* This class is about learning Resourceful development :D

The Pro's have it! But it isn't always the right choice. Keep an open mind about the tools you or your team use. Try to be **Technically Agnostic** - the best engineers are.

# Updating a Form


# Writing the Script


# Responding With JSON


# Git Commit

Test your code and commit it to the current branch (ajax).
