---
layout: post
title: Booktrackr Part VI - Book Resource Endpoints
excerpt: Adding REST endpoints for the BOOK entity
date: 2016-05-02
comments: true
published: false
---

We're finally at the point that we can start building out some functionality of our application. In this installment of the Booktrackr series, we'll create REST endpoints for the `Book` entity along with unit and integration tests.

## `Book` Resource Endpoint Implementation

Many web apps are, at their heart, a thin layer overtop of a database to enforce some domain-specific logic. Booktrackr is not too much different, and the back end will really just provide CRUD (create, read, update, and delete) operations for books. We'll take a look at implementing the testing the `/books` endpoint, and you can look at the implementation for the other operations in the pull request that accompanies this post.

### The `BookRepository`

In order to perform those CRUD operations, we'll need a way to access the database. That's pretty easy to do with Spring Data. If you've worked with data access in Spring before, you know the concept of a _repository_ is central to data access, and Spring Data is no different. It provides a number of parameterized type interfaces that it can dynamically generate query and data access code for. Our `BookRepository` will look like this:

```java
@Repository
public interface BookRepository extends JpaRepository<Book, Long> {
}
```

That's all it takes to set up an object and perform basic data access in Spring data. Extending `JpaRepository` will provide CRUD database operations along with paging and sorting. You can check out the Javadoc for `JpaRepository` [here](http://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/JpaRepository.html). The `@Repository` annotation is a Spring annotation that tells the Spring container to make an instance of `BookRepository` available for injection into classes that depend on it.

### The `BookService`

I like to put a service layer in between data access code and controllers. While not strictly necessary, I like it for two main reasons:

* Performing database access from a controller requires you to open a database connection for the duration of the web request, when in reality the database connection only needs to be open for however long it takes to actually read or write the needed entities.
* Performing domain access in a separate class allows encapsulation of the data fetching logic. For example, suppose that our app is popular enough that the amount of users puts some load on the database. A popular way to alleviate that is to cache the data in memory, and only fetch data from the database if the data is not in the cache. The service is a good place to put that caching logic to keep classes focused...controllers should receive and respond to web requests, and data access code should talk to the database, so the service is a nice middle layer to keep those two layers nice and clean.

Here's what our `BookService` will look like:

```java
@Service
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

You see that `bookRepository` has a `findAll()` method that does what it sounds like it does, that we get for free by using Spring data. At runtime, Spring Data generates an implementation for this method, which is inherited from `JpaReposotory`, so we didn't have to write any code for it.

As far as the annotations go:

* `@Service` works similarly to `@Repository` and lets Spring know to make an instance of this class available for injection
* `@Autowired` tells Spring that when it is creating an instance of `BookService`, it should inject an instance of `BookRepository` into it
* `@Transactional(readOnly = true)` is part of Spring's [declarative transaction management](http://docs.spring.io/autorepo/docs/spring/4.2.x/spring-framework-reference/html/transaction.html) and has two parts. By itself `@Transactional` tells Spring that when this method is invoked, the Spring container should open a database connection, and then write any changes necessary upon completion of the method. Spring does this without you having to acquire, open, or close the connection, which is quite nice. The `readOnly = true` part tells Spring that this particular operation will not modify data, so Spring can optimize the flush mode of the transaction for some performance improvements. It also declares intent and can safeguard against persistent data modification, although that's an ancillary benefit and not `readOnly`'s primary purpose.

### The `BookController`

Ok, now that we've implemented the ability to load all `Book`s from the database, we can set up a controller to handle requests. Spring makes this easy to do as well:

```java
@RestController
public class BookController {

    private final BookService bookService;

    @Autowired
    public BookController(BookService bookService) {
        this.bookService = bookService;
    }

    @RequestMapping("/books")
    public ResponseEntity<List<Book>> getAllBooks() {
        return ResponseEntity.ok(bookService.getAllBooks());
    }
}
```

Pretty straightforward with a few things to note:

* The class annotation `@RestController` does two things: it lets Spring know that this is an `@Controller`, so the Spring container will look at the `@RequestMapping` annotations to match HTTP request paths to these handler methods. It is also equivalent to annotating each method's return type with `@ResponseBody` which automatically serializes the response to JSON, so we can just focus on our application logic.
* `@RequestMapping("/books")` tells Spring MVC that when it receives an HTTP request for the path `/books`, invoke the `getAllBooks()` method on `BookController`.

## Testing Our Implementation

Some of you may have heard of Test Driven Design, or TDD, which advocates writing tests before implementation. I think it's a useful technique and has it's place, but I also don't think that writing tests after implementation is "wrong." In short, my testing philosophy is to have tests for things that should be tested, and to use judgement about what should be tested and whether to write those tests before or after you implement the logic.

In short, have good test coverage for things that matter.

### How to Test Controllers

Even though our `getAllBooks()` endpoint only relies on a couple of classes, there's actually quite a bit going on. A lot of it is abstracted away by Spring, which is both a blessing and a curse. At a high level, here's everything that goes on to handle a request to our `/books` endpoint:

1. Parse the HTTP request
2. Look up the handler mapping to see which method should be invoked to handle a request to `/books`
3. Invoke the `getAllBooks()` method on `BookController`
4. Open a database connection
5. Load all the rows in the `books` table of the database
6. Close the database connection
7. Convert the rows from the `books` table into a `List` of `Book` objects (i.e. a `List<Book>`)
8. Convert the `List<Book>` into JSON
9. Send the JSON back in an HTTP response that the user's browser or mobile device can parse



### Unit Test

### Integration Tests





- had to add jayway to testCompile classpath
- difference between integration testing and unit testing for controllers
