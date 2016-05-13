---
layout: post
title: Booktrackr Part X - Account Creation
excerpt: Implementing a create account endpoint
date: 2016-05-12
comments: true
published: true
---

Now that we have a `User` object, we need a way for users to create accounts, so we'll implement that functionality in this post. This is very similar to the [part 6 post for `Book` endpoints](https://ryanpmartz.com/booktrackr-part-06-book-endpoints), so I'm just going to cover the highlights and point out some things along the way. Let's get into it.

## Building a Create User Endpoint

### `UserController`

One difference between the create user workflow when compared to what we did for our create book workflow is that we're going to use a `SignupDto` in the create user workflow. "DTO" stands for _data transfer object_ and is just a container for some data, sometimes called a data class. They're commonly used in Java for serializing and deserializing objects to JSON.

There are two reasons we're using a DTO for the signup form:  we want the form to have both a `password` and `confirmPassword` and validate that they can match as well as we want the ability to mark the `password` field on `User` objects to be ignored when serializing the objects to/from JSON.

Here's the `UserController`:

```java
  private final UserService userService;
  private final PasswordEncoder passwordEncoder;

   @Autowired
   public UserController(UserService userService, PasswordEncoder encoder) {
       this.userService = userService;
       this.passwordEncoder = encoder;
   }

   @RequestMapping(value = "/users", method = RequestMethod.POST)
   public ResponseEntity<?> createUser(@Valid @RequestBody SignupDto signupDto) throws JsonProcessingException {
       if (!signupDto.getPassword().equals(signupDto.getConfirmPassword())) {
           Map<String, String> errorJson = new HashMap<>();
           errorJson.put("field", "confirmPassword");
           errorJson.put("error", "Password and confirm password must match");

           ObjectMapper mapper = new ObjectMapper();

           return ResponseEntity.status(HttpStatus.BAD_REQUEST)
                   .contentType(MediaType.APPLICATION_JSON_UTF8).body(mapper.writeValueAsString(errorJson));
       }

       final String requestedEmail = signupDto.getEmail();

       Optional<User> existingUserOptional = userService.getUserByEmail(requestedEmail);
       if (existingUserOptional.isPresent()) {
           return ResponseEntity.badRequest().body(EMAIL_EXISTS_MESSAGE);
       }

       User user = new User();
       user.setEnabled(true);
       user.setEmail(signupDto.getEmail());
       user.setFirstName(signupDto.getFirstName());
       user.setLastName(signupDto.getLastName());
       user.setPassword(passwordEncoder.encode(signupDto.getPassword()));

       signupDto.setPassword(null);
       signupDto.setConfirmPassword(null);

       User savedUser = userService.createUser(user);

       return ResponseEntity.status(HttpStatus.CREATED)
               .contentType(MediaType.APPLICATION_JSON_UTF8).body(savedUser);
   }
```

You'll notice that we're checking to make sure that users cannot sign up with duplicate emails.

The `passwordEncoder` field requires Spring Security. We'll go into securing the app a little bit more in the next post, but the point is that you should **never, ever, under any circumstances, store a user's password in plain text.**

#### Blogs vs. Reality

Ideally you'd want some kind of verification mechanism as part of signup to verify that the user owns the email that they're signing up with. This is so that people cannot sign up using other peoples' email addresses and cause them to get emails from sites they never signed up for.

I'm trying to keep this series pretty realistic, but I do intend to make a working finished copy available. Because of that, I'm deliberately skipping the email verification part to prevent people from spamming it. Since our application doesn't send any emails, this isn't a huge deal, but I did want to point out that this is a less than desirable signup process.

## Wrap Up

And that's about it for our create account workflow. It's pretty simple, but then again so is Booktrackr. You can play with the endpoints in the Swagger UI. Next time will be a longer post but it should just about wrap up the back end, save for a few tweaks that may come up as we implement that web and browser clients.

### Resources

* [Pull Request](https://github.com/rpmartz/booktrackr/pull/9/files)
