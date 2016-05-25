---
layout: post
title: Booktrackr Web Client Part III - Automated JS Testing
excerpt: Test infrastructure for Angular.js and hooking Gulp into Gradle
date: 2016-05-24
comments: true
published: true
---

Now that we have a bare bones AngularJS app setup, we need to set up some more testing infrastructure so we can make sure we have client side tests as well. Furthermore, we need to integrate the execution of those tests into our Gradle build so that those client tests get executed on our continuous integration server. 

### More NPM Dependencies

You should be a pro at this by now. Look at the changes in `package.json` in the pull request that accompanies this post to see which dependencies you need to install. 

### Adding Gulp Tasks

We are going to add three new Gulp tasks: `bower`, `test`,  and `itest`. 

The `bower` task will just call `bower install` and is needed so that Bower dependencies are installed as part of the Gradle build. This is nice because it keeps the build portable; whether it's a new developer checking out the project or  the CI server, all bower dependencies will be installed during the build. 

The `test` task will use the [Karma Test Runner](https://karma-runner.github.io/0.13/index.html) to run our Javascript unit tests so that we can test our AngularJS logic. There's not much now other than a fake math test, but we'll be adding to it later on as we build out the client side functionality. Karma works by reading a `karma.conf.js` file that for Booktrackr is located in `src/test/javascript`. 

Finally, the `itest`, short for _integration test_ task will run end-to-end tests using the AngularJS testing tool Protractor. Similar to Karma, Protractor reads its configuration from a `protractor.conf.js` file, located in `src/integration-test/javascript`. Protractor tests are integration tests because they use a browser (either a real one or a headless one like PhantomJS) to run test against an actual running application. 

### Hooking the Front End Tasks into the Gradle Build

Now that we have some more fancy gulp tasks, we want these to be execute as part of our regular test suite when we build and deploy the application. There's some additional complexity, however, since our project is now multi-language: Java and Javascript. Thankfully, Gradle is a flexible tool with a lot of plugins, and if we add the `com.moowork.gradle-gulp-plugin` we can have Gradle download Node and NPM for us and then specify gulp tasks to run as part of the build. Check out the changes to the `build.gradle` to see a full diff of the changes required. 

## Wrap Up

In this post, we added a few more Node modules that will make our workflow a little bit easier and make our project more portable. We also updated our Gradle build to execute our front end unit tests, so now when we push commits, Travis CI will run our Java test suite and our Javascript test suite. 

#### Resources

* [Pull Request](https://github.com/rpmartz/booktrackr/pull/14)
