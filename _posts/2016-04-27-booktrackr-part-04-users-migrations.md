---
layout: post
title: Booktrackr Part IV - Our First Entity
excerpt: The one in which we create our first entities and set up database migrations.
date: 2016-04-29
comments: true
published: true
---

So far, we've [set up our project](https://ryanpmartz.com/booktrackr-getting-started) and added [continuous integration](https://https://ryanpmartz.com/booktrackr-part-02-continuous-integration) and [automated deployment to Heroku](https://ryanpmartz.com/booktrackr-part-03-first-deployment), which is swell, but Booktrackr still doesn't, you know, _actually do anything_. So we should probably fix that.

We're going to fix that by adding our first domain entity, a `Book` object, along with all of the scaffolding and boilerplate we need to actually persist it to the database. Speaking of the database, we're going to migrate that from an in memory H2 database to a no kidding PostgreSQL database.

The amount covered per post will start to pick up now, and this post in particular is going to be a bit dense. I'm trying to try to hit the sweet spot between brevity and thoroughness.  In the _Resources_ section at the end of each post I'll post links for further reading and a link to the pull request containing all changes discussed in the post.

## Persistence

### Hibernate and Postgres

Despite what the internet and NoSQL vendors would have you believe, relational databases are still a good choice as a persistence store to back a REST API. We're going to use [PostgreSQL](http://www.postgresql.org/) as our database, and we're going to use [Hibernate](https://hibernate.org) by way of [Spring Data](http://docs.spring.io/spring-data) to communicate with the database.

Hibernate is an implementation of the Java Persistence API (JPA) specification, which, loosely defined, is a specification for mapping rows in a relational database to Java classes. You can check out the [Hibernate docs here](http://http://hibernate.org/orm/), a [JPA Tutotorial here](https://docs.oracle.com/javaee/6/tutorial/doc/bnbpz.html), and the [Spring Data JPA docs here](http://docs.spring.io/spring-data/jpa/docs/1.10.1.RELEASE/reference/html/).

### Switching to Postgres

Right now our application uses the embedded H2 database. This is an in-memory database implemented in Java, so it's convenient to develop with and works out of the box with Spring Boot as long as you have H2 on your classpath. The problem is that you would almost never want to use H2 in production. Moreover, it's a [generally accepted software development best practice](http://12factor.net/dev-prod-parity) to keep development and testing environments as similar to production as possible, and when you can. Since we're going to use Postgres as our production database, we need to use it for development and testing as well.

#### Installing Postgres

You've got a few options when it comes to using Postgres...you can install it locally, use a service, or do what I prefer and use Docker.

##### Non-Docker Options

If you want to install Postgres onto your development computer, you can [follow the instructions for downloading installing Postgres here](http://www.postgresql.org/download/). Right now, Heroku Postgres uses version 9.4.6, so get that if you can.

You can also use a cloud service like [Amazon RDS](https://aws.amazon.com/rds/postgresql/) or [Elephant SQL](https://www.elephantsql.com/) if you'd like to avoid installing software.

##### Docker

[Docker](https;//docker.com) is software for running lightweight containers. What that means is beyond the scope of this series, but there are some nice advantages to using Docker. The principal advantage is that it makes it easy to run the identical version of a Docker container on your local machine, the continuous integration server, and production, which means you can be more confident that how your application runs locally is how it runs in production.

The easiest way to get started with Docker is to use the [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox) unless you develop on a Linux machine that can run containers directly.

Go ahead and download and install the Docker Toolbox, and then use the Kitematic program to download a Postgres Docker Image. You'll need to add a `POSTGRES_USER`, `POSTGRES_PASSWORD`, and `POSTGRES_DB` environment variables in the Kitematic console and start/restart the Postgres container.

##### Update `build.gradle`

We can change H2 driver dependency to the `testCompile` scope and add the Postgres driver in the `compile` scope in `build.gradle`:

```groovy
compile('org.postgresql:postgresql:9.4.1208')
testCompile('com.h2database:h2:1.4.191')
```

That's going to allow us to use Postgres for development but run the integration test suite against an embedded database, so the suite will run faster. Using a different database for testing is not ideal, and we'll address that, but having some integration tests is still better than having none and we'll update Travis CI to run our integration tests against Postgres later.

## Updating Booktrackr Config for Postgres

In addition to replacing H2 with Postgres, we need to update our application's configuration in order to be able to connect to our Postgres database.

### Update `application.properties`

Spring Boot supports a [lot of options for external configuration](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html), but I like to use [YAML](http://yaml.org/) in order to be able to nest properties. So go ahead and rename `application.properties` to `application.yml`.

Now we need to actually add the connection info to the configuration file. We need to add a datasource URL and username and password info.

```yaml
spring:
  datasource:
    url: ${BOOKTRACKR_DB_URL}
    username: ${BOOKTRACKR_DB_USERNAME}
    password: ${BOOKTRACKR_DB_PASSWORD}
```

Spring Boot will flatten the YAML into Java properties format it needs, e.g. `spring.datasource.url`. The values are environment variables. I like to put config in the environment for a couple of reasons:

* It allows you to keep your configuration stored in source control without exposing any sensitive information
* It explicitly documents what environment variables need to be set in order to run the application
* Configuration is stored in the environment, so deploys can be identical across development, staging, test, and production environments.

On my machine, Kitematic's IP address is `192.168.199.100`, so the the `BOOKTRACKR_DB_URL` should be set to something like `jdbc:postgresql://192.168.99.100:32785/db`, depending on what port Kitematic has exposed on the Docker VM.

I've found it's easiest to set the environment variables in the Run Configuration within IntelliJ and just run `BooktrackrApp.main()` directly from the IDE, but you're welcome to set the environment variables in your `~/.bashrc` and run the compiled JAR from the command line as well.

### Adding a Test Configuration

If you tried to run the Gradle `build` or `test` tasks, you saw that our changes have broken the build. Specifically, they're causing our one test to fail, which means that our Spring container is not starting.

This is happening because we've put Postgres on the classpath. Spring Boot sees this, and looks for Postgres connection information in the properties files. It sees that we have the `spring.datasource.url` property declared, but if there's no value for `${BOOKTRACKR_DB_URL}`, an exception occurs.

We can fix this temporarily by adding an empty `src/test/resources/application.yml` file, which overrides the `application.yml` in `src/main/resources`. That will cause Spring to use the embedded H2 database that's on the test classpath for the integration tests and keep our tests passing for the time being.

### Our First Entity

We're not out of the proverbial woods with respect to configuration just yet, but we can finally do something that resembles application development. Since the application is named Booktrackr, it stands to reason that the first entity we create is a `Book` entity.

Here's what our annotated `Book` class looks like:

```java
@Entity
@Table(name = "book")
public class Book implements Serializable {

    private static final long serialVersionUID = 1L;

    @Id
    private Long id;

    @Column(nullable = false)
    private String title;

    @Column(nullable = false)
    private String author;

    @Column @Lob
    private String notes;

    // getters and setters ...
```

All of the annotations are from the `javax.persistence` package. This tells Hibernate what database table to find the data for `Book` classes in, the primary key of that table, and how to map Java class fields to database columns.

We're going to keep our domain objects overly simple, which means that our schema will be extra flat. There will be wailing and gnashing of teeth from the database geeks out there that our schema is not in [Boyce-Codd normal form](https://en.wikipedia.org/wiki/Boyce%E2%80%93Codd_normal_form), and they're right, but that's ok. This is an application development exercise, not a database normalization exercise. Just like _real life_, we're going to exercise judgement about when we'll spend the time and effort to conform to "The One Right Way" and when we'll make simplifying assumptions so we can deliver something that works. Accordingly, rather than having a fully normalized schema, we're going to keep the `author` and `notes` on the `Book` object itself.

By making these simplifications, we're buying ourselves ease of development. The cost is technical debt that might need to be paid back down the line. For example, pretend our application takes off and has hundreds of thousands of users...at that point maybe we do care that we are storing the multiple records for the same book. But that's a bridge best crossed upon arrival.

### Managing the Schema

Now that we've got a `Book` entity, we need a `books` table to store our books in. We've got a few options here.

#### Letting Hibernate Create the schema

 We can configure Hibernate to create your schema for you by setting the `spring.jpa.hibernate.ddl-auto` property to `create`. This is a pretty handy feature of Hibernate, because it will read your JPA annotated entities and automatically create the correct database schema for them. Furthermore, when you set the value of this property to `validate`, it will ensure that the schema that your application is pointing to matches what you expect based on your entities.

#### Schema Migrations

The downside of using Hibernate to create your schema every time you modify an entity is that this is not how you deploy your application to production. You cannot just drop your database every time you need to modify something about your schema.  

This is where database schema migrations come in. If you've built web apps using Django or Rails, then you're familiar with the concept. We'll be using a tool called [Flyway](https://flywaydb.org) to manage our database migrations. With Flyway, you actually deliver your migration SQL scripts with your application, and Flyway will use a table in the database to keep track of which migrations need executed. This also gives developers and ops people the opportunity to see what version of the schema is actually deployed, in addition to ensuring that schema migrations are applied in a consistent, repeatable manner.

##### The `Book` migration

One of the reasons I like Flyway is that it uses SQL, so you get the opportunity to write and see DDL, which helps you understand concepts like constraints, indexes, and foreign keys that Hibernate would otherwise just manage behind the scenes. The downside of this is that it can take a few tries to get the DDL right.

Here's the DDL for our first table:

```sql
CREATE TABLE book
(
    id BIGINT PRIMARY KEY NOT NULL,
    author VARCHAR(255) NOT NULL,
    notes TEXT,
    title VARCHAR(255) NOT NULL
);
```

Go ahead and put that in a file called `src/main/resources/db/migration/V1__add_book_entity.sql`. Flyway will see that, check the `schema_version` table in the database (or create one if it doesn't exist), and apply that migration if it doesn't already exist.

One little trick I've picked up is to use two development databases or schemas until you know what SQL the different JPA annotations result in. So let's say you have two databases, `db1` and `db2`. You can point your application at `db1` with the property `spring.jap.hibernate.ddl-auto = create`, and then use command line or GUI database administration tools to look at the DDL. You can then copy and paste the entire DDL statements, or the portions that you need, into your migration file. You can then change the `spring.jap.hibernate.ddl-auto` property back to `validate` restart the application pointing at `db2`, and verify that the migration is correct.

## Wrap Up

In this installment of the Booktrackr series we migrated from the embedded H2 database to Postgres, added database schema migration, and created our first `Book` entity.

### Part V Preview

In part V we'll update our Heroku configuration to use Postgres and we'll fix the fact that Travis still runs the integration test suite using H2.

### Resources

* [Pull Request for this post's changes](https://)
* [Hibernate](http://hibernate.org)
* [Spring Data JPA](http://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
* [Flyway](https://flywaydb.org/)
