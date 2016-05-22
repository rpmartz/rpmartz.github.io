---
layout: post
title: Booktrackr Web Client Part II - AngularJS Setup
excerpt: In Part II of the web client series we'll set up Angular.js
date: 2016-05-22
comments: true
published: true
---

Last time we installed the main tools we'll need for working with front end assets, NodeJS and Gulp. This time around, we'll install some Gulp plugins and implement the first vestiges of our AngularJS Booktrackr web client. 

### Install AngularJS

Since we took the time to set up [Bower](http://bower.io/), installing AngularJS is pretty simple. We simply run `bower install angular angular-route —save`, and Bower will download those to `src/main/resource/static/bower_components`. 

###  A Minimal AngularJS app

#### The `index.html` 

The first part of creating an AngularJS application is to define the main page: 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Booktrackr</title>
    <!-- bower:css -->
    <!-- endbower -->

    <!-- inject:css -->
    <!-- endinject -->
</head>
<body ng-app="booktrackrApp">
<div class="container" ng-view></div>
<!-- bower:js -->
<!-- endbower -->

<!-- inject:js -->
<!-- endinject -->
</body>
</html>

```

The things to note here are the `ng-app` directive on the `body` tag and the `ng-view` directive on the `<div>` element More on the `inject` and  `bower` comments later. 

####  The `app.js`

Now we need to set up our Angular app. Create a file `src/main/resources/app/js/app.js`:

```javascript
(function () {
    'use strict';

    angular.module('booktrackrApp', ['ngRoute']);

    angular.module('booktrackrApp').config(['$routeProvider', function ($routeProvider) {
        $routeProvider
            .when('/', {
                templateUrl: 'partials/home.html',
                controller: 'HomeController',
                controllerAs: 'vm'
            });


    }]);

})();

```

A few things to note:

* We're wrapping this in an [IIFE](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression) in order to prevent global namespace clashes. This will help us keep our sanity when we start to concatenate files.  
* We're using the Angular setter syntax (`angular.module('booktrackrApp', ['ngRoute']);`) to declare our module
* We're adding a `/` route that will render the page `/partials/home.html` and is controlled by `HomeController`. There's nothing special about those two files, but have a look at them in the pull request so you can see how they work.

A great resource for understanding how to write good Angular code is [John Papa's Angular Style Guide](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md), which I highly recommend you check out. 

### Configure Gulp to Inject required JS files. 

Let's set up a little bit more infrastructure to inject both third party and our custom front end assets so that this gets done automatically. 

#### Install Dependencies 

We need to install a few more Node modules so that we can set up some new tasks in our `gulpfile.js`:

* `gulp-angular-filesort`
* `gulp-inject`
* `run-sequence`
* `wiredep`

Remember to pass `—save` when you run `npm install` for those modules so that they get saved to the `package.json` file. 

#### Update `Gulpfile.js`

##### The `wiredep` task.

Wiredep is a Node module that will read your `bower.json` file and inject the required files into our `index.html` file. This is useful because if we add a new dependency, we don't need to remember to add another `<script>` or `<link>` tag into our DOM. Furthermore, as we'll see later in the series, we can also use `wiredep` in conjuction with other Node modules to minify and concatenate our front end assets. 

Here's what the `wiredep` task looks like in `gulpfile.js`:

```javascript
gulp.task('wiredep', function () {
    return gulp.src('src/main/resources/static/index.html')
        .pipe(wiredep({
            exclude: [
                'bower_components/bootstrap/dist/js/' // use ui-bootstrap
            ],
            overrides: { // see discussion here https://github.com/twbs/bootstrap/issues/16663
                bootstrap: {
                    main: [
                        "dist/css/bootstrap.css"
                    ]
                }
            }
        }))
        .pipe(gulp.dest('src/main/resources/static'));
});
```

We're excluding the Bootstrap Javascript because it doesn't play nicely with Angular, and due to some changes in the way Bower processes resources (see [here](https://github.com/twbs/bootstrap/issues/16663) for more discussion), we have to override Bootstrap's configuration to inject the CSS files. 

##### The `inject` task

`gulp-inject` is similar to the `wiredep` task but this one will inject the JS and CSS files that we write into our `index.html`. Here's how the task is configured:

```javascript
gulp.task('inject', function () {
    return gulp.src('src/main/resources/static/index.html')
        .pipe(inject(
            gulp.src('src/main/resources/static/app/js/**/*.js')
                .pipe(angularFilesort()), {relative: true}
        ))
        .pipe(gulp.dest('src/main/resources/static'));

});
```

Two things of note are that we are using the `angularFilesort()` function so that the files will be injected in the order they need to be for the application to bootstrap itself correctly. The other thing to note is that we are using `relative: true` so that the paths to the resources will work right with Spring Boot's static resource handling. 

Make sure you look at the `gulpfile.js` on GitHub or the changes in the pull request that accompanies this post to get all of the changes.

## Wrap Up

The only other thing we need to do is update our `SecurityConfig` class to permit unauthenticated requests to our JS files, which you can look at in the PR. If you run the `wiredep` and `inject` tasks and then run the application, you should see the markup from `home/partials.html`. 

### Resources

* [Pull Request](https://github.com/rpmartz/booktrackr/pull/12)
* [Angular Style Guide](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md) 
