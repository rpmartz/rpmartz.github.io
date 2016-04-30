---
layout: post
title: Booktrackr Part V - Heroku on Postgres and Travis
excerpt: Updating Travis CI and Heroku to use Postgres
date: 2016-04-30
comments: true
published: true
---

Booktrackr is staring to resemble something that you can use to track books, even if only because there's now a `Book` entity. But we've got some problems we've got to fix.

The first problem is that we can't deploy to Heroku right now without a Postgres database. The second problem is that our tests on the continuous integration server still use the H2 embedded database, even though our application now uses Postgres.

But do not fret; we are living in the golden age of software development, and modern services like Heroku and Travis CI make problems like this easy to fix.

## Using Postgres for Testing on Travis CI

### Update `.travis.yml`

We need to update Booktrackr's `.travis.yml` file to use Postgres during the build process:

```yaml
services:
  - postgresql
before_script:
  - psql -c 'create database booktrackr_test;' -U postgres
addons:
  postgresql: "9.4"
```

That's all it takes. That config tells Travis to use a Postgres 9.4 database and make it available on the build machine before starting the Gradle build.

### Update Travis CI

In the last post I mentioned that using environment variables makes it easy to deploy your app to different environments, and here you'll see why. First, delete `src/test/resources/application.yml` so that the build will use `src/main/resources/application.yml` during the tests.

Now, go to _Settings_ for your Booktrackr build and add values for the `BOOKTRACKR_DB_URL` (`jdbc:postgresql://localhost:5432/booktrackr_test`) and `BOOKTRACKR_DB_USERNAME` (`postgres`). You don't need a password for the `postgres` user unless you added one to the `before_script` command in `.travis.yml`, but if so go ahead and add it as the `BOOKTRACKR_DB_PASSWORD` environment variable.

## Using Postgres on Heroku

### Update `build.gradle`

Since we've set up Travis CI to run our tests, it doesn't make sense to have the full test suite run as part of deployment to Heroku; that will just take extra time during deployments. We just need to have Heroku build an executable artifact, so go ahead and update `build.gradle` to have the `stage` task depend on `bootRepackage` rather than a full `build`:

```groovy
task stage {
	dependsOn bootRepackage
}
```

### Add Postgres

It's similarly easy to update Heroku to use Postgres. In your [Heroku dashboard](https://dashboard.heroku.com) look at the _Add-ons_ sections. Search "Postgres" and choose the "Hobby Dev - Free" plan.

### Add configuration

It may take a few minutes to provision your database, but you can head over to [Heroku Postgres Dashboard](https://postgres.heroku.com/databases). You can click on the name of the database and it will show you the configuration info.

In a new tab, open up your Booktrackr app and go to the _Settings_ tab. You can click on the "Reveal Config Variables" and see that Heroku has already added the database as an enivronment variable. You'll need to add the same variables here that Booktrackr expects that we added on Travis, but make sure they're the values from the Heroku Postgres dashboard.

### Verify Booktrackr Deploys

Once you've added the database info to Booktrackr, you can go ahead and deploy to Heroku and verify that the app starts by tailing the logs in your terminal.

## Wrap Up

After a monstrous Part IV, this was a little bit of a shorter part. But we made some important progress by making sure that our test suite runs in an environment similar to the production deployment.

### Resources

* [Pull Request](https://github.com/rpmartz/booktrackr/pull/4)
* [Travis CI Postgres Setup Guide](https://docs.travis-ci.com/user/database-setup/#PostgreSQL)
* [Heroku Postgres Docs](https://devcenter.heroku.com/categories/heroku-postgres)
