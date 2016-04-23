---
layout: post
title: Booktrackr Part I: Getting Set Up
comments: true
published: true
---

## Getting Help

We'll get started building our application in just a second, but first a quick word for those following along with the series and working on it. This is not a comprehensive "how to" series that will hold your hand through every step. The format of the posts will generally be an overview of the important concepts followed by highlights of configuration and implementation. The full source code is [available on GitHub]().

If I gloss over something that doesn't make sense, try to do some Googling to figure it out yourself; most of the tools are open source and exhaustively documented. If you're still stuck, get in touch by leaving a comment, opening a Github issue, or sending me a note and I'll do what I can to get you back on track.

## Prerequisite Software

* A Java IDE installed. I recommend [IntelliJ IDEA](https://www.jetbrains.com/idea/) but Netbeans or Eclipse will work fine
* [git](https://git-scm.com/) for version control. Don't even think about developing software without using version control. Ever.
* A Java 8 Java Development Kit (JDK)

## Spring

There are several excellent choices in the JVM ecosystem with which to build RESTful web services, including [Spring](https://spring.io/), [Dropwizard](http://www.dropwizard.io/), [Play Framework](https://www.playframework.com/), and a number of other [microframeworks](http://blog.takipi.com/java-micro-frameworks-the-new-trend-you-cant-ignore/). Spring, Dropwizard, and Play are all battle tested and have been adopted by large companies for good reason. We'll be using Spring to build Booktrackr.

The second most popular internet past time for software developers is to hate on how "enterprise-y" and "heavyweight" Java is. That may or may not have been accurate at some point in Java's history, but with the release of Java 8 and the emergence of frameworks like Spring Boot, you might be surprised how developer-friendly modern Java development can be.

### Spring Boot

Spring is a popular Java framework used by cutting edge technology organizations like Netflix and stodgy enterprise shops like your bank. Although no one's going to mistake Spring for a minimalist framework, the folks at Pivotal have done an outstanding job providing a "convention over configuration" option that just works. In their own words:

> Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can "just run". We take an opinionated view of the Spring platform and third-party libraries so you can get started with minimum fuss. Most Spring Boot applications need very little Spring configuration.

In my experience, Spring Boot lives up to the hype. Let's dive in.

### Spring Boot Starter

Spring Boot even provides an "Initalizr" that will generate a `pom.xml` or Gradle build script including the dependencies you select. To start with, we'll just use the "Web" and "JPA" dependencies. I chose Spring Boot 1.4.0M2 and Gradle as my build tool.

Go ahead and let Initializr generate the project. Uncompress the zip file and move it out of your downloads folder.

#### Add persistence

The only other thing left to do before we have a working, albeit minimal, application is add a database connection library. For the time being, we'll add an embedded database, which Spring Boot can configure itself to use by looking at the libraries on the classpath. Add the line

```groovy
compile('com.h2database:h2:1.4.191')
```

to the `dependencies` section your `build.gradle`, and we should be all set.

## Gradle

Gradle is a build and dependency management tool for Java and Android projects. A build tool allows development teams to make builds across machines and manage dependencies. It ensures that that every build is executed the same way, and then specified versions of third party libraries are downloaded and packaged with your application.

The two most popular build tools for Java are Maven and Gradle. Maven is fine but, let's be honest, XML sucks. We'll be using Gradle for this project.

### The Gradle Wrapper

One thing I'd like to point out is the `gradlew` and `gradle.bat` scripts along with the `gradle` directory. This is a pretty cool feature of Gradle, called the Gradle wrapper, that Spring Boot configured for us. It includes a version of Gradle that you use just like a locally installed instance of Gradle, except that you invoke the commands with `./gradlew` instead of the `gradle` command.

There's a couple of benefits to using a wrapper instead of a locally installed version of your build tool:

*  your build tool is versioned with the application, so it's one less piece of software collaborators need to install before they get to hacking on your project
* because everyone uses the same version of of the build tool, it eliminates the possibility for "works on my machine" anomalies caused by different point releases of the same tool
* no installation of a build tool necessary to set up a continuous integration server

Oh, and if you just so happen to love you some XML, are a new tool Luddite, or are stuck with Maven on an existing project because you're too busy building software to quibble about your build tool, there's a [Maven wrapper](https://github.com/takari/takari-maven-plugin), too.

#### A first smoke test

Let's kick the tires and make sure that what Spring Initializr built for us actually works by running the test suite. In the root of the project, go ahead and run `./gradlew test`. It'll take a minute the first time because Gradle has to download all your dependencies, but when the tests pass we can be confident that our build is in a good state. You should see something like:

![Passing Tests](/public/images/booktrackr/initial_test-terminal_output.png)

## Our First Commit

Now that we have a working project, we're in pretty good shape to go ahead and commit this. The last thing we need to do is add a `.gitignore` to make sure we're not putting generated sources or binary artifacts in our repository. I used the `Gradle.gitignore` from [GitHub's gitignore repository](https://github.com/github/gitignore) and added `.idea/` and `*.iml` to it, which are generated artifacts created by IntelliJ.

## Wrap Up

That wraps it up for part I of the Booktrackr series. We set up our environment and used Spring Boot's Initializr to generate a Gradle project for us, and added the H2 embedded database for us to get started with.

### Part II Preview

In Part II, we'll talk about continuous integration and we'll set our project up to use it.

### Resources

* [Spring Boot Docs](http://projects.spring.io/spring-boot/)
* [Gradle Docs](https://docs.gradle.org/current/release-notes)
