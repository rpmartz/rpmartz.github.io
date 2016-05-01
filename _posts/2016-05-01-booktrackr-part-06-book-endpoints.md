---
layout: post
title: Booktrackr Part VI - Book Resource Endpoints
excerpt: Adding REST endpoints for the BOOK entity
date: 2016-05-02
comments: true
published: false
---

We're finally at the point that we can start building out some functionality of our application. In this installment of the Booktrackr series, we'll create REST endpoints for the `Book` entity along with unit and integration tests.

## `Book` Resource endpoints

Many web apps are, at their heart, a thin layer overtop of a database to enforce some domain-specific logic. Booktrackr is not too much different, and the back end will really just provide CRUD (create, read, update, and delete) operations for books.

### The `BookRepository`

In order to perform those CRUD operations, we'll need a way to access the database. That's pretty easy to do with Spring Data. If you've worked with data access in Spring before, you know the concept of a _repository_ is central to data access, and Spring Data is no different. It provides a number of parameterized type interfaces that it can dynamically generate query and data access code for. Our `BookRepository` will look like this:

```java
@Repository
public interface BookRepository extends JpaRepository<Book, Long> {
}
```

That's all it takes to set up an object and perform basic data access in Spring data. Extending `JpaRepository` will provide CRUD database operations along with paging and sorting. You can check out the Javadoc for `JpaReposotry` [here](http://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/JpaRepository.html).

### The `BookService`

I like to put a service layer in between data access code and controllers. While not strictly necessary, I like it for two main reasons:

* Performing database access from a controller requires you to open a database connection for the duration of the web request, when in reality the database connection only needs to be open for however long it takes to actually read or write the needed entities.
* Performing domain access in a separate class allows encapsulation of the data fetching logic. For example, suppose that our app is popular enough that the amount of users puts some load on the database. A popular way to alleviate that is to cache the data in memory, and only fetch data from the database if the data is not in the cache. The service is a good place to put that caching logic to keep classes focused...controllers should receive and respond to web requests, and data access code should talk to the database, so the service is a nice middle layer to keep those two layers nice and clean.

Here's what our `BookService` will look like:

```java
public class BookService {

    private final BookRepository bookRepository;

    @Autowired
    public BookService(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }

    @Transactional(readOnly = true)
    public List<Book> getAllBooks() {
        return bookRepository.findAll();
    }
}
```


- add repository and service (why service)
- had to add jayway to testCompile classpath
- difference between integration testing and unit testing for controllers
