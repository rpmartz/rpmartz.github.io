---
layout: post
title: Booktrackr Part II - Continuous Integration
comments: true
published: true
---

Let's talk about the how and why to use continuous integration, and set up Booktrackr to use it.

## CI for the Working Programmer

[Continuous integration](https://www.thoughtworks.com/continuous-integration) (or just "CI" as the cool kids call it these days) is a generally accepted software development best practice. Books have been written, blog posts abound, and there are quite literally people who make their living as "CI consultants." Check out the link above for some good resources on the topic, but like everything, CI is subject to the 80/20 rule, so don't go too crazy.

### A Working Definition

In its simplest incarnation, CI is really just about having a central server that runs a test suite every time someone checks in code. I know that you, like me, have _never_ accidentally checked in broken code, but not all devs are as diligent as we are. (That was sarcasm in case it didn't come through). Humans forget; computers don't. CI tests every commit and catches problems early, ideally right after the changes are committed. That way, the offending commits should be relatively small and the developer should have a good idea of what just changed to minimize the time spent debugging. Or so the theory goes.

### Assumptions, Ramifications, and Benefits

Obviously, a CI server is really only as good as the tests it runs. **To earn the benefits of CI, you have to invest the time in writing tests and writing the right kinds of tests.**

A common argument against having automated integration tests or end-to-end/functional tests is that they take too long to run. There is a lot of merit to keeping your test suite fast in order to keep the feedback loop on changes quick, but offloading tests that take longer to run onto a CI server can help alleviate some of the costs of extensive testing. Developers can just run unit tests, which should be fast, locally, and allow the CI server to run tests that take longer to run. Modern CI platforms can also help with this by running builds or parts of builds in parallel.

The cooler, more mature older brother of CI is continuous delivery, or CD. Continuous delivery, loosely speaking, means that every commit (or merged pull request) to `master` results in a new version of the software being deployed. Obviously CD requires a thorough and exhaustive pipeline of tests, in addition to other health and status checks of the recently-provisioned servers. Reasonable people might argue that there should always be a human in the loop as a final sanity check before releasing a new build to users. That's not an unreasonable check to implement, especially for teams new to CD. Even so, a good CD pipeline can do everything other than balance the new server into the load balancer, freeing up developers to focus on other things. But you'll never achieve continuous delivery until you get continuous integration right.

## CI for Booktrackr

Hopefully you're convinced that time spent setting up CI and writing the right kinds of tests is an investment that will continue to yield dividends throughout the life of the project by catching defects earlier and allowing you to build and release software more quickly and more confidently.

If you're not convinced, that's too bad, because we're still going to set up CI for Booktrackr.

The good folks over at [Travis CI](https://travis-ci.org) have graciously made their service available for free to all open source projects, so we'll use them for our CI servers. They've done all the hard work of setting up the environment, so we can reap the benefits of CI with less initial investment of time and effort.

**Note: make sure you go to the travis-ci.org site and not the .com site for the version that supports open source projects**

### The `.travis.yml` File

Travis CI is [super easy to start using](https://docs.travis-ci.com/user/getting-started/). You simply sign in with your GitHub credentials, tell them which repositories you want them to keep an eye on, add a `.travis.yml` to your repository, then push changes to your repository to trigger your first CI build.

Here's what my `.travis.yml` looks like:

```yaml
language: java
before_cache:
  - rm -f $HOME/.gradle/caches/modules-2/modules-2.lock
cache:
  directories:
    - $HOME/.gradle/caches/
    - $HOME/.gradle/wrapper/
jdk:
  - oraclejdk8
```

You need to add the bit about Oracle JDK 8 because for some reason the default Java version was 1.7. The Travis server will detect the `gradlew` file and can take it from there.

### What is Being Tested?

An astute observer, or a contrarian, might ask what's really being tested right now. And it would be a good question, because the answer is, "not much, yet." The only test right now tests that the application can start, i.e. that the Spring context is configured correctly. While it's not terribly useful now, it will be when we start adding other Spring components.

## Wrap Up

In this installment of the Booktrackr series we set up continuous integration, so every commit that we push to GitHub will be tested by our automated test suite.

### Part III Preview

In part III we'll set up Heroku and deploy our application for the first time. 

### Resources

* [Thoughtworks CI Page](https://www.thoughtworks.com/continuous-integration)
* [Travis CI Docs](https://docs.travis-ci.com)
