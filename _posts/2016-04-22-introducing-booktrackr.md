---
layout: post
title: Introducing Booktrackr
comments: true
published: true
---

I'm starting a new series that will chronicle building a modern Java application, called Booktrackr. Like it sounds, Booktrackr will be a way to keep track of books you've read along with notes on those books.

### Functionality

In order to limit the scope of this project, I'm deliberately limiting the scope of the functionality. In the context of this series of blog posts, here's the functionality I'm committing to building:

* Ability for new users to create accounts
* Ability for users to add books they've read
* Ability for users to add keep notes on books they've read using [Markdown](https://daringfireball.net/projects/markdown/)

That's it. There's certainly a lot more we could add to an application like this, but limiting the scope defines the finish line and keeps things simple.

### Architecture

Since [mobile is eating the world](http://a16z.com/2014/10/28/mobile-is-eating-the-world/), our application needs to be mobile ready. That means two things: the web client must be responsive and the backend must be able to be consumed by native mobile clients as well as browsers. Here's what Booktrackr's stack will look like:

* [Gradle](http://gradle.org/) as a build tool
* [Postgres](http://www.postgresql.org/) for persistence
* [Spring Boot](http://projects.spring.io/spring-boot/) for the back end
* [AngularJS](https://angularjs.org/) for the web application
* [Swift](https://swift.org/) for the native mobile application
* [Heroku](https://heroku.com) for hosting

We'll also use a number of libraries and third party services for things like monitoring, testing, continuous integration, and deployment.

### Goals

What's the point of this little exercise?

* Demonstrate what I consider to be a developer-friendly workflow for a modern Java application. I've been playing around with [jHipster](http://jhipster.github.io/) and want to incorporate many of those concepts into an app that I build, as opposed to something that's generated, in order to gain a deeper understanding of how all those pieces fit together.
* Learn to build a Swift app that communicates with a remote server. I've build iOS and Android apps before, but none that communicate with a remote service.
* Provide some value to folks struggling with how to put all the pieces together. There are bits and pieces of tutorials all over the place, and a lot of them are great, but there doesn't seem to be much in the way of a comprehensive, start-to-finish guide on how to build, deploy, and monitor an application. Whether you're a college senior wondering what apps in the "real world" look like or someone who's been learning to code on your own but want an example of how it all fits together, I hope you get some value out of this.
