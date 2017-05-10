1. Bootstrap npm and expressjs - hello world
  Add templating engine - hello world
1. Index projects with hard coded data in file - projects-index.handlebars
1. Add MongoDB
1. Create a new project - projects-new.handlebars
1. Show project - projects-show.handlebars
1. Edit/Update a project
1. Push to Heroku



We've been putting all our logic in one file `app.js`, except our views. Now we are going to create a new folder called `models`, and create a file in there called `project.js`. This is where we'll keep our model logic for our Project resource.

```bash
$ mkdir models
$ cd models
$ touch project.js
$ cd .. # TO RETURN TO THE ROOT FOLDER OF OUR PROJECT
```
