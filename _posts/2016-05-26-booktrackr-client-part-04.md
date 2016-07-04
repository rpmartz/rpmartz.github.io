---
layout: post
title: Booktrackr Web Client Part IV - Implementing Client Side Authentication 
excerpt: Time to start implementing some useful functionality in our Angular app.
date: 2016-07-05
comments: true
published: true
---

Finally we can start to have some fun and implement some functionality in Booktrackr's web client. 

### A Quick Productivity Enhancement 

Before we dive into the fun part, we need to make a quick addition to our `build.gradle` that will make our lives easier - or at least our workflow more productive. Spring Boot comes with an optional module called DevTools. It makes live reloading of static resources faster in addition to some optimizations around restarts of compiled code. Just add the `"org.springframework.boot:spring-boot-devtools"` JAR as a compile time dependency and Spring Boot will autoconfigure itself. When launched from the command line as a JAR, Spring Boot does not launch DevTools so you can be confident you're using it for development only. 

### Signup Functionality

The first thing we need to do is add the ability to sign up for Booktrackr. This is a pretty straightforward process…we've already implemented the `/users` endpoint on the back end of Booktrackr, so it's just adding the functionality to the Angular client. 

#### The `signup.html` Partial

Angular's built in `$http` client makes it pretty easy to serialize a form and send it to a RESTful server as JSON, and we can use the Angular Messages module to display validation errors in the form to the user, which can enhance user experience. 

Here's what the input field for the user's email on the signup form looks like:

```html
<div class="form-group"
     ng-class="{ 'has-error' : (newUserForm.email.$invalid && newUserForm.email.$touched) || validationErrors.email }">
    <label class="control-label" for="email">Email</label>
    <input type="email" ng-model="signup.newUser.email" class="form-control" id="email" name="email"
           placeholder="Email"
           ng-required="true" ng-minlength="6" ng-maxlength="255">
    <div class="help-block" ng-messages="newUserForm.email.$error" ng-if="newUserForm.email.$touched">
        <p ng-message="minlength" class="text-danger">Please enter your email address.</p>
        <p ng-message="maxlength" class="text-danger">Email is too long.</p>
        <p ng-message="required" class="text-danger">Please enter your email address.</p>
        <p ng-message="email" class="text-danger">Please enter a valid email address.</p>
        <p ng-message="used" class="text-danger">This email is already in use.</p>
    </div>
    <div class="help-block" ng-messages="validationErrors.email"
         ng-if="newUserForm.$submitted && validationErrors.email">
        <p ng-message="result" class="text-danger">{{ validationErrors.email.result }}</p>
    </div>
</div> 
```

It's not the most terse HTML in the history of markup, but Angular Messages takes care of handling the validation logic pretty transparently. 

The controller that renders this partial communicates with the back end through the `AuthenticationService`, which you can check out in the pull request; that's all pretty standard Angular. 

### Logging In and Managing JWTs Client Side

Now that users have the ability to create an account, they can log in. When a user logs in, the server sends a JWT back to the client in the form of an `Authorization` header. The client then stores this token and sends it with every request in order for the server to make an authentication and/or authorization decision. [Auth0](https://auth0.com/) has published a nice module that handles storing the JWT on the client side, so all we need to do is add an interceptor in `app.js` to check for that token and add it to each request: 

```javascript
 angular.module('booktrackrApp').factory('authInterceptor', function ($rootScope, TokenService) {

        var authInterceptor = {
            request: function (config) {
                var currentUserToken = TokenService.getCurrentUserToken();
                var token = currentUserToken ? currentUserToken : null;

                if (token) {
                    config.headers.authorization = token;
                }
                return config;
            },
            response: function (response) {
                if (response.status === 401) {
                    $rootScope.$broadcast('unauthorized');
                }
                else if (response.status == 403) {
                    $rootScope.$broadcast('forbidden');
                }
                return response;
            }
        };
        return authInterceptor;
    });
```

Ignore the HTTP 401 and 403 responses for now, we'll discuss how to handle those later. This interceptor will check for and add the `Authentication` header to each outgoing request.  

Users can now create accounts using their email addresses and then use those accounts to log in. Next time, we'll write some tests for the already-existing functionality using Angular's build in testing. 

### Resources

* [Pull Request](https://github.com/rpmartz/booktrackr/pull/15)

* [Angular Messages](https://docs.angularjs.org/api/ngMessages/directive/ngMessages)

* [Angular Storage](https://github.com/auth0/angular-storage)

  ​