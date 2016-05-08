---
layout: post
title: Booktrackr Part VIII - Monitoring the App
excerpt: Configuring Monitoring
date: 2016-05-08
comments: true
published: false
---

In this post we'll set up some lightweight monitoring and metrics infrastructure and plug that into a visualization service.

It should go without saying, but let me be clear about something. Our app doesn't have any performance problems right now. Heck, it barely even _performs_. You may have heard the "premature optimization is the root of all evil" trope before, and that's a good rule of thumb. You never want to spend time optimizing something until it's a known, measured, demonstrable bottleneck in your application.

The reason we want to set up monitoring _now_ is to make sure we have a good baseline to observe when certain request paths or method calls start to deviate from their normal performance characteristics. Just like with testing, CI, and automated deployment, we do that in the beginning of the project to avoid having the temptation to not do this later because "it's too complicated." Since our app is simple, we can add this stuff and evolve it with our app. It's much easier to just add monitoring to new features as we build them than it is to wait until we have 25,000 lines of code and we have to figure out what and where to monitor things.

## Spring Boot Metrics

Spring Boot comes with a feature called the [Spring Boot Actuator](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#production-ready) that will expose a lot of your application's metrics via an HTTP endpoint.

Here are some of the things that adding the Spring Boot Actuator makes available:

* An auto configuration report about which defaults Spring Boot did or did not apply
* A complete list of all Spring beans in the application
* All of your application's `@RequestMapping`s
* System monitoring info like average system load, heap size, threads, data source usage

### Configuring Actuator

In order to get everything that Spring Boot Actuator provides, all you need to do is add the Spring Boot Actuator starter to `build.gradle` in the `dependencies` block: `compile("org.springframework.boot:spring-boot-starter-actuator")`.

You can then poke around the different endpoints like `/health`, `/metrics`, `/beans`, or `/mappings` and see the information that gets returned. You may want to pretty-print it in an editor like Sublime Text or Atom so you can actually see what gets returned. Here's what the `/health` endpoint returns:

```json
{  
   "status":"UP",
   "diskSpace":{  
      "status":"UP",
      "total":499071844352,
      "free":414050349056,
      "threshold":10485760
   },
   "db":{  
      "status":"UP",
      "database":"PostgreSQL",
      "hello":1
   }
}
```

That lets us know that the application is up, we have plenty of disk space available, and the database is working and the app can connect to it. Pretty cool for one line in our build script. 

## Metrics

## Metrics Visualization

## Wrap Up

So thanks to the hard work that others have done we now have an auto-documenting API that we can use to both view and interact with our API.

### Resources

* [Pull Request](https://github.com/rpmartz/booktrackr/pull/6)
* [Spring Boot Actuator Docs](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#production-ready)
