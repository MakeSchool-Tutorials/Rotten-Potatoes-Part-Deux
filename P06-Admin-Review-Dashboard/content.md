---
title: "Admin Review Dashboard"
slug: admin-dashboard
---

Uh-oh - trolls have been posting stupid and repetitive reviews to your database, so your team is requesting an **admin dashboard** to review and quickly delete them.

# New Feature Branch

Following the best practice of using feature branches, we're going to create a new feature branch to add an admin dashboard.

```bash
$ git checkout -b admin-dash
$ git branch
```

# Admin Dashboard

Remember to say Resouceful. What is an admin dashboard? It is a list of reviews right? That means it should live here: `/reviews` and the code should be in `reviews.js`. Well maybe, or it could live here `/admin` and we could make a new controller called `admin.js`.

# Git Commit

Test your code and commit it to the current branch (ajax).
