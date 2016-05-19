---
layout: post
title: Booktrackr Web Client Part I - Build Setup
excerpt: Setting up tools for working with front end assets. 
date: 2016-05-19
comments: true
published: true
---

Over the last few posts we built a working API with endpoints to sign up, authenticate, and manage our books, but all we have now is our Swagger API documentation tool to actually interact with it. 

It's time to start building the web client, and we'll begin that by setting up some front end tools that will help improve our workflow and keep us sane when working with front end assets. 

A word of warning about working with front end assets: there's a lot of different ways to do so. The web development ecosystem is thriving right now and there are a lot of tools out there and opinions about which one is best, with more tools and opinons being generated daily. I'm not asserting that this way or these tools are the best, they're just one way to get the job done. So don't get your jeans all in a twist if you prefer Grunt or hate Angular. They're just tools. 

Let's dig in.  

### Overview 

At a high level, just like we set up a build process for the API, we are going to do the same thing for our front end assets. I'm deliberately using the term _build process_ to include compilation, tests, and packaging here. We'll use different tools for the front end assets than we do for building the API layer, but the ideas and concepts will be more or less the same. 

### Update `.gitignore`

Just like we ignore all of the generated artifacts from the Gradle build process, like `.class` files for example, we are going to ignore things that our front end build pipeline creates or can recreate during the build process. The only thing we really want in version control are front end assets that we write. If someone else wants to use the project, he or she can simply clone the repository and use these tools to install things. Just like Gradle will download all the Java dependencies that the API depends on, our front end tools will download all the front end assets we need. 

Go ahead and add lines for `node_modules/` and `bower_components/` to `.gitignore` and we will be taken care of for now. 

### NPM Init

**You need [Node.js](https://nodejs.org/en/) and the [Node Package Manager](https://www.npmjs.com/) installed for all of the rest of these steps.** If you don't have those installed on your local computer, go ahead and install those using the instructions on the Node.js web site. 

The Node Package Manager (called `npm` or just NPM from here on out), is a command line tool for working with Node modules. 

#### NPM Init and the `package.json`

 Just like Gradle uses a `build.gradle` file for instructions on what and how to build the project, NPM uses a `package.json`. As its name implies, the `package.json` is a JSON file that can contain scripts to run as well as the Node modules that the app depends on. 

You can make your own, but it's better to just let NPM create it for you. From the root of `booktrackr` (i.e. the same directory as the `.git` directory), go ahead and run `npm init`. You can take a look at the `package.json` in the pull request to see what I chose for defaults, as well as the one you just generated. 

### Bower

The first NPM package we install will be [Bower](http://bower.io/), a package manager for the web. Bower describes itself on its homepage as:

> Bower works by fetching and installing [packages](http://bower.io/search) from all over, taking care of hunting, finding, downloading, and saving the stuff you’re looking for. Bower keeps track of these packages in a manifest file, [`bower.json`](http://bower.io/docs/creating-packages/#bowerjson)

#### Installing Bower

To install it, from the root of Booktrackr, run `npm install bower —save`. You'll notice that Bower gets installed in the `node_modules` directory and Bower is added to the `dependencies` section of `package.json`. 

##### The `.bowerrc`

Now we need to customize where Bower installs its dependencies, which we can do by creating a `.bowerrc` file and adding this to it:

```json
{
  "directory": "src/main/resources/static/bower_components"
}
```

#### Installing Bootstrap via Bower

Let's take our fancy new front end package manager for a spin by installing [Bootstrap](https://getbootstrap.com). Run `bower install bootstrap —save` and notice that it installs Bootstrap in `src/main/resources/static/bower_components` and updates the `bower.json` with the dependency information. 

### Gulp

The last tool we need to install is one called [Gulp](http://gulpjs.com/), which is a task runner for front end assets. You'll need to actually install the `gulp-cli` package globally using the command `npm install --global gulp-cli`. Once you do that, go ahead and run `npm install --save-dev gulp` to install Gulp locally and save it as a dependency to the `package.json`. 

#### A minimal `gulpfile.js`

Just like the `build.gradle` tells Gradle what tasks to run, Gulp relies on a `gulpfile.js` to run its tasks. We'll be building ours up over the next few posts, but for now we can make a pretty simple one just to test that everything's working correctly:

```javascript
var gulp = require('gulp');

gulp.task('default', function() {
  console.log('Gulp is working.');
});
```

You should then be able to run the `gulp` command in the same directory as `gulpfile.js` and see the output of the `console.log` statement, so you know Gulp is set up.


## Wrap Up

That's it for this post. Nothing too complicated this time around, just installing some tools that will make our life easier later, and make working with front end assets more portable, testable, and repeatable. 

### Resources

* [Pull Request](https://github.com/rpmartz/booktrackr/pull/12)
