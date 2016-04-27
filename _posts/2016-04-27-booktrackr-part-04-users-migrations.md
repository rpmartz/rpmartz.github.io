---
layout: post
title: Booktrackr Part IV - Our First Entity
excerpt: The one in which we create our first entities and set up database migrations.
comments: true
published: false
---

So far, we've [set up our project](https://ryanpmartz.com/booktrackr-getting-started) and added [continuous integration](https://https://ryanpmartz.com/booktrackr-part-02-continuous-integration) and [automated deployment to Heroku](https://ryanpmartz.com/booktrackr-part-03-first-deployment), which is swell, but Booktrackr still doesn't, you know, _actually do anything_. So we should probably fix that.

We're going to fix that by adding our first domain entity, a `Book` object, along with all of the scaffolding and boilerplate we need to actually persist it to the database. Speaking of the database, we're going to migrate that from an in memory H2 database to a no kidding PostgreSQL database.

The amount covered per post will start to pick up now, and this post in particular is going to be a bit dense. I'm trying to hit the sweet spot between brevity and thoroughness.  In the _Resources_ section at the end of each post I'll post links for further reading and a link to the pull request containing all changes discussed in the post.

## Persistence

### Hibernate and Postgres

Despite what the internet and NoSQL vendors would have you believe, relational databases are still a good choice as a persistence store to back a REST API. We're going to use [PostgreSQL](http://www.postgresql.org/) as our database, and we're going to use [Hibernate](https://hibernate.org) by way of [Spring Data](http://docs.spring.io/spring-data) to communicate with the database.

Hibernate is an implementation of the Java Persistence API (JPA) specification, which, loosely defined, is a specification for mapping rows in a relational database to Java classes. You can check out the [Hibernate docs here](http://http://hibernate.org/orm/), a [JPA Tutotorial here](https://docs.oracle.com/javaee/6/tutorial/doc/bnbpz.html), and the [Spring Data JPA docs here](http://docs.spring.io/spring-data/jpa/docs/1.10.1.RELEASE/reference/html/).

### Switching to Postgres

Right now our application uses the embedded H2 database. This is an in-memory database implemented in Java, so it's convenient to develop with and works out of the box with Spring Boot as long as you have H2 on your classpath. The problem is that you would almost never want to use H2 in production. Furthermore, it's a [generally accepted software development best practice](http://12factor.net/dev-prod-parity) to keep development and testing environments as similar to production as possible, and when you can. Since we're going to use Postgres as our production database, we need to use it for development and testing as well.

#### Installing

You've got a few options when it comes to using Postgres...you can install it locally, use a service, or do what prefer and use Docker.

##### Docker

[Docker](https;//docker.com) is software for running lightweight containers. What that means is beyond the scope of this series, but there are some nice advantages to using Docker. The principal advantage is that it makes it easy to run the identical version of a Docker container on your local machine, the continuous integration server, and production, which means you can be more confident that how your application runs locally is how it runs in production.

The easiest way to get started with Docker is to use the [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox) unless you develop on a Linux machine that can run containers directly. 

##### Non-Docker Options

If you want to install Postgres onto your development computer, you can [follow the instructions for downloading installing Postgres here](http://www.postgresql.org/download/). Right now, Heroku Postgres uses version 9.4.6, so get that if you can.

You can also use a cloud service like [Amazon RDS](https://aws.amazon.com/rds/postgresql/) or [Elephant SQL](https://www.elephantsql.com/) if you'd like to avoid installing software.

#### Configuring our App

### Our First Entity

Since the application is named Booktrackr, it stands to reason that the first entity we create is a `Book` entity.



### Setting Up Migrations

####

## Wrap Up

In this installment of the Booktrackr series we set up our deployment pipeline and deployed the first version of our application to Heroku.

### Part IV Preview

In part IV we'll add our first domain objects and database schema migration.

### Resources

* [Hipern](https://github.com/rpmartz/booktrackr/pull/2)
* [Heroku guide to deploying Spring Boot apps](https://devcenter.heroku.com/articles/deploying-spring-boot-apps-to-heroku)
