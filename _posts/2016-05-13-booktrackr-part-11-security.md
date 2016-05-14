---
layout: post
title: Booktrackr Part XI - Security
excerpt: Securing Booktrackr using Spring Security
date: 2016-05-14
comments: true
published: true
---

Booktrackr now has REST endpoints for `Book`s and an endpoint to create a new account. The only major task left for the back end is to secure it. We arguably could have integrated security from the beginning, and if the API were bigger that probably would have been prudent so that we could update and test the security of new endpoints as we built them.

This will be a longer post, but it will just about wrap up the back end of Booktrackr and we can move on to the web and mobile clients. Let's jump in.

## Booktrackr Security Overview

### JSON Web Tokens

We're going to use a [JSON Web Token](https://jwt.io/), or JWT (pronounced "jot"), as our authentication mechanism. JWTs are a [draft IETF standard](https://tools.ietf.org/html/rfc7519) that are one way to perform stateless authentication. If you've used Bearer Tokens or API keys before, you've likely used JWTs or a similar scheme before.

Traditional web applications often use form login with a session key to perform authentication. This is fine for most web applications and it's easy enough to put sessions in something like Redis or DynamoDB to get around session affinity and scalability concerns, but the form login approach with a finite session does not transfer well to mobile applications. Since we want one authentication scheme for both our web and mobile client, we're going to use JWTs for both.

We're going to use the JJWT library, created by the folks at [Stormpath](https://stormpath.com), to generate and validate JWTs. Go ahead and add `io.jsonwebtoken:jjwt:0.6.0` as a `compile` dependency to your `build.gradle` file.

### The Authentication Workflow




## Configuring Spring Security

We added the `spring-boot-starter-security` to `build.gradle` when we were implementing the create account functionality, so there's no dependencies to add. Like with the rest of the starters in Spring Boot, when the application starts up, it scans the classpath for certain dependencies and configures itself.  We also added a `SecurityConfig` last time, which was a bit misleading because what we actually did was make sure that Spring Security did not protect any request paths. At any rate, we're going to fix it now.



### Resources

* [Pull Request](https://github.com/rpmartz/booktrackr/pull/9/files)
