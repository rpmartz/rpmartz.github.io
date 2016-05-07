---
layout: post
title: Booktrackr Part VII - Documenting the API
excerpt: Documenting the API endpoints
date: 2016-05-07
comments: true
published: true
---

[Last time](https://http://ryanpmartz.com/booktrackr-part-06-book-endpoints), we implemented methods for creating, reading, updating, and deleting books. We've still got some things to do before the Booktrackr back end is complete, but most of the operations involved with book interactions are set up.

But we need to document those endpoints to that the people who didn't write the application can read them. You'll come across a lot of people who claim to be agile and therefore don't do documentation, or assert that their code is their documentation. Both of those ideas are a bunch of malarkey. Yes, a working application is more important than documentation, but the best application in the world doesn't do anyone any good if only the developer and people with access to the source code can figure out how to use it.

So we're going to do things the right way and document our API, from the beginning. This will have the added benefit that we'll have an in-browser way to interact with our application even though we haven't built a UI for it yet.

## Introducing Swagger

There are a couple of good options out there for documenting REST APIs, but we're going to use [Swagger](http://swagger.io/) by way of the excellent [Springfox](http://springfox.github.io/springfox/docs/current/) library.

### Update `build.gradle`

In order to use these libraries, we need to add the Springfox and Springfox UI libraries as dependencies to our `build.gradle` file:

```java
compile('io.springfox:springfox-swagger2:2.4.0')
compile('io.springfox:springfox-swagger-ui:2.4.0')
```

### Create config

Now we need to configure Swagger to read the Spring annotations and generate our API docs. We could put this in `SpringApplication`, but I like to keep configuration of different items in separate classes. So I created a new package called `config`, and made a class called `ApiDocumentationConfig`:

```java
@Configuration
@EnableSwagger2
public class ApiDocumentationConfig {

    @Bean
    public Docket apiDocketConfiguration() {
        ApiInfoBuilder apiBuilder = new ApiInfoBuilder();
        apiBuilder.title("Booktrackr API Documentation");

        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiBuilder.build())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.ryanpmartz.booktrackr.controller"))
                .paths(PathSelectors.any())
                .build();
    }
}
```

That's all it takes to generate basic Swagger API documentation, but if you've ever integrated with an API before you know that this is better than what you usually get.

### Accessing the Swagger UI

You can access the Swagger UI at `/swagger-ui.html`. You can also use the Swagger UI to interact with the application, sending HTTP requests and viewing the responses that the server returns, which is super handy since we don't have a fancy user interface just yet.

Here's what the Swagger UI looks like:

![Swagger UI](https://s3.amazonaws.com/ryan.martz/blog/images/booktrackr/swagger-ui.png)

## Wrap Up

So thanks to the hard work that others have done we now have an auto-documenting API that we can use to both view and interact with our API.

### Resources

* [Pull Request](https://github.com/rpmartz/booktrackr/pull/6)
