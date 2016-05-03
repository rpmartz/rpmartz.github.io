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

I should also note that it allows you to add methods to the interface and can generate queries in addition to the basic operations it provides, as long as your method names conform to the rules Spring Data expects.

### The `BookService`

I like to put a service layer in between data access code and controllers. While not strictly necessary, I like it for two main reasons:

* Performing database access from a controller requires you to open a database connection for the duration of the web request, when in reality the database connection only needs to be open for however long it takes to actually read or write the needed entities.
* Performing domain access in a separate class allows encapsulation of the data fetching logic. For example, suppose that our app is popular enough that the amount of users puts some load on the database. A popular way to alleviate that is to cache the data in memory, and only fetch data from the database if the data is not in the cache. The service is a good place to put that caching logic to keep classes focused...controllers should receive and respond to web requests, and data access code should talk to the database, so the service is a nice middle layer to keep those two layers nice and clean.

Here's what our `BookService` looks like:

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

You see that `bookRepository` has a `findAll()` method that does what it sounds like it does, that we get for free by using Spring data. At runtime, Spring Data generates an implementation for this method, which is inherited from `JpaRepository`, so we didn't have to write any code for it.

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

Some of you may have heard of Test Driven Design, or TDD, which advocates writing tests before implementation. I think it's a useful technique and has its place, but I also don't think that writing tests after implementation is "wrong." In short, my testing philosophy is to have tests for things that should be tested, and to use judgement about what should be tested and whether to write those tests before or after you implement the logic.

In short, Testing: do it. For things that should be tested.

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

Others have written about testing _ad nauseam_, so I don't want to get too deeply into it, but I do want to talk a little bit about testing our application. Unit tests are one aspect of testing and test bits of code in isolation. In this specific case, that means that a unit test should not depend on database access; it should just test the controller's behavior given some set of inputs.

We have two options to unit test the controller. One is to treat it as a Plain Old Java Object (POJO), and inject mock implementations of `BookService`, its one dependency, and verify that it returns the list of `Book` objects that the `BookService` returns. The test would involve creating a new controller and testing it, e.g. `BookController controller = new BookController(bookService);`, and then invoking the `getAllBooks()` method.

The upside of this approach is that it keeps the test simple, and fast. The downside is that that isn't how this method is actually invoked. In real life, this method will be invoked by Spring MVC in response to HTTP requests to the path `/books`. Our method is simple, but other methods will take JSON as input or may use validation rules invoked by Spring that we'd like to test.

Luckily, the good folks at Spring have provided some really nice and constantly-improving utilities for testing Spring web apps and Spring Book applications.

One of those testing goodies is their `MockMvc` context. The `MockMvc` context gives you options: you can use it to stand up a no kidding embedded servlet container and run actual tests against a full Spring web application, or you can use the `MockMvc` `standaloneSetup` to test controllers "in isolation." I use the term "in isolation" loosely because using `MockMvc` is kind of a middle ground between a unit test and an integration test. It's not quite a full integration test, but it sets up a mock servlet context that makes it a little bit more of an integration test.

What's nice about it is that you actually test it by sending HTTP requests to the request mappings, so you can test that your `@RequestMapping`s are set up correctly, and any validation you do using Spring's built in validators can be tested, along with the response generation and serialization.

Let's take a look at one of these standalone MVC tests looks like:

```java
public class BookControllerTest {

    private MockMvc mockMvc;

    @Mock
    private BookService bookService;

    @InjectMocks
    private BookController bookController;

    @Before
    public void setUp() {
        MockitoAnnotations.initMocks(this);
        mockMvc = standaloneSetup(bookController).build();
    }

    @Test
    public void testGetAllBooks() throws Exception {
        Book firstBook = new Book();
        firstBook.setId(1L);
        firstBook.setAuthor("John Doe");
        firstBook.setTitle("The First Book");
        firstBook.setNotes("Some notes");

        Book secondBook = new Book();
        secondBook.setId(2L);
        secondBook.setAuthor("Jane Doe");
        secondBook.setTitle("The Second Book");
        secondBook.setNotes("Read this after the first book");

        when(bookService.getAllBooks()).thenReturn(Arrays.asList(firstBook, secondBook));

        mockMvc.perform(get("/books")).andExpect(status().isOk())
                .andExpect(content().contentType(MediaType.APPLICATION_JSON_UTF8))
                .andExpect(jsonPath("$[0].title").value("The First Book"))
                .andExpect(jsonPath("$[1].title").value("The Second Book"))
                .andDo(print());
    }

}
```

Note that you'll have to add the Jayway `json-test` library as a `testCompile` dependency in order to make assertions about the response JSON.

You can see that we're still using a mock `BookService` by way of the Mockito testing library to keep the test reasonably fast. The test is still slower than a pure unit test, but faster than a test that requires a full-on Spring container to be stood up and requires real database access. In exchange for a slightly slower test, we get to actually send HTTP requests to our controller to make sure that the request mappings and request types are correct, and that Spring correctly serializes the response to JSON.

### Integration Tests

`BookControllerTest` is a kind of unit-test-on-steroids that's better than a unit test, but not quite a full integration test. Integration tests are more comprehensive than unit tests; they can test that all of your Spring annotations and configuration are doing what you expect, that your application can talk to the database, etc.

There's a lot of testing value in integration testing, arguably more so than in unit testing since integration tests more closely approximate how the application will work in production. The drawback of integration tests is that they're slower than unit tests because the entire application, including the Spring context, has to start up, and they talk to the database, which adds additional latency. You can, however, delegate integration tests to your continuous integration server so that developers can just run unit tests. 




- had to add jayway to testCompile classpath
- difference between integration testing and unit testing for controllers
