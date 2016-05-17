---
layout: post
title: Booktrackr Part XI - Security
excerpt: Securing Booktrackr using Spring Security
date: 2016-05-17
comments: true
published: true
---

Booktrackr now has REST endpoints for `Book`s and an endpoint to create a new account. The only major task left for the back end is to secure it. We could have, and arguably should have, integrated security from the beginning, and if the API were bigger that probably would have been prudent so that we could update and test the security of new endpoints as we built them.

This will be a longer post, but it will just about wrap up the back end of Booktrackr and we can move on to the web and mobile clients. Let's jump in.

## Booktrackr Security Overview

### JSON Web Tokens

We're going to use a [JSON Web Token](https://jwt.io/), or JWT (pronounced "jot"), as our authentication mechanism. JWTs are a [draft IETF standard](https://tools.ietf.org/html/rfc7519) that are one way to perform stateless authentication. If you've used Bearer Tokens or API keys before, you've likely used JWTs or a similar scheme before.

Traditional web applications often use form login with a session key to perform authentication. This is fine for most web applications and it's easy enough to put sessions in something like Redis or DynamoDB to get around session affinity and scalability concerns, but the form login approach with a finite session does not transfer well to mobile apps. Since we want one authentication scheme for both our web and mobile client, we're going to use JWTs for both.

We're going to use the JJWT library, created by the folks at [Stormpath](https://stormpath.com), to generate and validate JWTs. Go ahead and add `io.jsonwebtoken:jjwt:0.6.0` as a `compile` dependency to your `build.gradle` file.

### The Authentication Workflow

If you don't understand how token authentication works, you should check out the links above and read over how it does, because the description below won't make too much sense without it.

In a nutshell, JWT authentication works very similar to form authentication. In form authentication, the user provides her username and password and, if the credentials are correct, receives a session id in response. The user's browser will send that session ID as a cookie with every subsequent response, and the server can use that session ID to retrieve information about that user's session. The difference is that under a JWT authentication scheme, rather than storing the information about the user's session in memory on the server, all necessary information is stored in the JWT itself. The issuing server crytographically signs the JWT, so other servers who share the JWT signing key can verify both that the JWT is authentic and that the user has not modified it in any way.

Let's take a look at ours:

```json
eyJhbGciOiJIUzUxMiJ9
.eyJzdWIiOiJtYXJ0enJwQGdtYWlsLmNvbSIsInVzZXJfaWQiOiI3MTAwMzZmMy0xMjYyLTQ0MDEtODdmMC0wN
DU2MWU2NDgwZTYiLCJyb2xlcyI6WyJST0xFX1VTRVIiXSwiZW1haWwiOiJtYXJ0enJwQGdtYWlsLmNvbSIsImV4cCI6MTQ2NDMyMTYwMH0
.9pXp--zfHSWeijlu9tWgYEQWrfDLM2Md7r8zUhd0pQ9p1fpFqkFOH7SU-
DkRb6ReRBvrdfOGiqFEzsxgrO0Idw
```

(Note: I put line breaks in that for rendering purposes but the actual value is a contiguous string)

And here's what the decoded JSON looks like:

```json
{"alg":"HS512"}
{"sub":"email@ryanpmartz.com","user_id":"710036f3-1262-4401-87f0-04561e6480e6","roles":["ROLE_USER"],"email":"email@ryanpmartz.com","exp":1464321600}
(signature)
```

As you can see the JWT body above has the user ID, users's roles, the token's expiration date and a signature to prove that the token is valid. The only way to get a signed token is to authenticate, so a server that receives a JWT whose signature passes can be confident that at some point the user authenticated and the token has not been modified. Say a malicious user tried to escalate his or her privileges by adding `ROLE_ADMIN` in the `roles` array. That would not work because upon receiving the token, the server would check that the signature matches the contents and see that the token's contents did not match the signature and the token was therefore modified. 

## Implementing JWT Authentication

### Working with JWTs

At a basic level, what we need is a way to convert information about our `User` object to and from a JWT in order to determine whether the user can access the resource he or she is requesting. We'll represent this as a `JwtAuthentictionToken` and need a `JwtUtil` class to work with these tokens:

```java
@Component
public class JwtUtil {

    private static final Logger log = Logger.getLogger(JwtUtil.class);

    private final String signingSecret;

    @Autowired
    public JwtUtil(@Value("${jwt.signing.secret}") String siginingSecret) {
        this.signingSecret = siginingSecret;
    }

    /**
     * Generates a JWT for the user
     *
     * @param user the user to generate a token for
     * @return a signed JWT that will be used for stateless authentication on subsequent requests
     */
    protected String generateToken(User user) {
        checkState(user);

        Claims claims = Jwts.claims().setSubject(user.getEmail());
        claims.put("user_id", user.getId());
        claims.put("roles", user.getRoles().stream().map(UserRole::getUserRole).collect(toSet()));
        claims.put("email", user.getEmail());

        LocalDate tenDaysFromNow = LocalDate.now().plusDays(10);
        Date expirationDate = DateUtil.toJavaDate(tenDaysFromNow);

        return Jwts.builder()
                .setClaims(claims)
                .setExpiration(expirationDate)
                .signWith(SignatureAlgorithm.HS512, signingSecret)
                .compact();
    }

    protected JwtAuthenticationToken tokenFromStringJwt(String rawJwt) {
        DefaultJwtParser parser = ((DefaultJwtParser) Jwts.parser());
        parser.setSigningKey(signingSecret);

        try {
            Jws<Claims> jws = parser.parseClaimsJws(rawJwt);
            Claims claims = jws.getBody();


            UUID userId = UUID.fromString((String) claims.get("user_id"));
            String email = ((String) claims.get("email"));
            Collection<? extends GrantedAuthority> roles = parseRolesFromClaims(claims);

            return new JwtAuthenticationToken(userId, email, roles);
        } catch (Exception e) {
            log.info(String.format("Exception occurred parsing JWT [%s].\nException message: %s", rawJwt, e.getMessage()));
            return null;
        }
    }

    private void checkState(User user) {
        Assert.notNull(user, "Cannot generate JWT from empty user");
        Assert.isTrue(user.isEnabled());
    }

    @SuppressWarnings("unchecked")
    private Collection<UserRoleEnum> parseRolesFromClaims(Claims claims) {
        List<String> roleValues = (List<String>) claims.get("roles");
        return roleValues.stream().map(UserRoleEnum::valueOf).collect(toSet());
    }
```

### Authenticating and Receiving a JWT

Now we need a way to receive a JWT. Conceptually this is identical to submitting a login form, but we're going to use a filter instead. Basically, the user will provide credentials in the HTTP request to the `/authenticate` endpoint, and if the credentials are valid, then the server will provide a JWT.

Here's the `JwtLoginFilter`:

```java
public class JwtLoginFilter extends AbstractAuthenticationProcessingFilter {

    private static final Logger log = Logger.getLogger(JwtLoginFilter.class);

    private final JwtUtil jwtUtil;
    private final UserDetailsService userDetailsService;

    public JwtLoginFilter(String defaultFilterProcessesUrl, JwtUtil jwtUtil, UserDetailsService userDetailsService, AuthenticationManager authManager) {
        super(defaultFilterProcessesUrl);

        this.jwtUtil = jwtUtil;
        this.userDetailsService = userDetailsService;

        setAuthenticationManager(authManager);
    }

    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException, IOException, ServletException {

        final LoginRequest login = new ObjectMapper().readValue(request.getInputStream(), LoginRequest.class);

        log.debug("Attempting authentication for username [" + login.getUsername() + "]");
        final UsernamePasswordAuthenticationToken loginToken = new UsernamePasswordAuthenticationToken(
                login.getUsername(), login.getPassword());

        return getAuthenticationManager().authenticate(loginToken);
    }

    @Override
    protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response,
                                            FilterChain chain, Authentication authResult) {

        log.debug("Successful authentication occurred. Auth name: " + authResult.getName());

        User authenticatedUser = (User) userDetailsService.loadUserByUsername(authResult.getName());
        String token = jwtUtil.generateToken(authenticatedUser);
        response.setHeader("Authorization", "Bearer " + token);

        SecurityContextHolder.getContext().setAuthentication(jwtUtil.tokenFromStringJwt(token));
    }
}
```

### Authenticating Subsequent Requests

So the previous section detailed how a user gets a JWT, now we need a way to authenticate a user's subsequent JWTs. Once the user authenticates and recieives a JWT, the client (i.e. the web or mobile app) will send the JWT in the request headers of subsequent HTTP requests. The user will parse the JWT and verify its validity (specifically that it has not expired or been modified), set the permissions for the user making the request, and then delegate authorization concerns to the normal Spring Security authorization process.

This is a pretty simple filter to implement by extending Spring's `GenericFilterBean`:

```java
@Override
   public void doFilter(ServletRequest req, ServletResponse response, FilterChain chain) throws IOException, ServletException {
       final HttpServletRequest request = (HttpServletRequest) req;

       log.debug("Attempting JWT Authentication");
       String header = request.getHeader("Authorization");

       if (header == null || !header.startsWith("Bearer ")) {
           log.debug("Missing or invalid Authorization header");
           chain.doFilter(request, response);
           return;
       }

       String authToken = header.substring(7);
       JwtAuthenticationToken token = jwtUtil.tokenFromStringJwt(authToken);

       SecurityContextHolder.getContext().setAuthentication(token);

       chain.doFilter(req, response);
   }
```

## Configuring Spring Security

We added the `spring-boot-starter-security` to `build.gradle` when we were implementing the create account functionality, so there's no dependencies to add. Like with the rest of the starters in Spring Boot, when the application starts up, it scans the classpath for certain dependencies and configures itself.  We also added a `SecurityConfig` last time, which was a bit misleading because what we actually did was make sure that Spring Security did not protect any request paths. At any rate, we're going to fix it now and integrate the JWT infrastructure that we just set up.

### Modifying `SecurityConfig`

#### Specifying security rules for request paths

We need to modify the `configure()` method of our `SecurityConfig` class like so:

```java
protected void configure(HttpSecurity http) throws Exception {
       http
               .csrf().disable()
               .authorizeRequests()
               // allow anonymous access access to Swagger docs
               .antMatchers("/v2/api-docs", "/**/swagger-ui.html", "/webjars/**", "/swagger-resources/**").permitAll()
               // anonymous users need to be able to log in
               .antMatchers("/authenticate").permitAll()
               // all other request paths are protected
               .anyRequest().authenticated()
               .and()
               .logout()
               .permitAll();
   }
```

The comments above the method calls explain why each rule is there. In a nutshell, this series of chained method calls disables Spring security's default CSRF protection (JWTs solve the CSRF problem), tell Spring Security not to protect the URLs for our API docs or authentication, and to require authentication for all other request paths.

#### The `UserDetailsService`

Spring Security requires a `UserDetailservice` to look up users. It comes with a few default implementations out of the box, but you can also opt to implement your own, which is what we are going to do:

```java
@Service
public class UserDetailsServiceImpl implements UserDetailsService {

    private final UserService userService;

    @Autowired
    public UserDetailsServiceImpl(UserService userService) {
        this.userService = userService;
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        Optional<User> result = userService.getUserByEmail(username);
        if (result.isPresent()) {
            User u = result.get();
            Hibernate.initialize(u.getRoles());

            return u;
        }

        throw new UsernameNotFoundException("No user found for username: " + username);

    }
}
```

Note that the return type of the `loadUserByUsername` method is `UserDetails`, so we need to make our `User` object implement the `UserDetails` interface. That's pretty straightforward and you can check the pull request to see how that's done.

#### Injecting our `UserDetailsService` into Spring Security

Now that we have an implementation of `UserDetailsService`, we can inject it as an `@Authowired` field into our `SecurityConfig` class and tell Spring to use it to look up users:

```java
@Override
public void configure(AuthenticationManagerBuilder auth) throws Exception {
   auth.userDetailsService(userDetailsService).passwordEncoder(bcryptEncoder());
}
```

#### Adding the JWT Filters

The last step is to add the JWT and authentication filters we configured above to the `SecurityConfig`. Inject instances of `JwtAuthenticationFilter` and `JwtUtil` into `SecurityConfig` as autowired class field. Then, at the end of the `configure(HttpSecurity security)` method, add these two lines:

```java
http.addFilterBefore(new JwtLoginFilter("/authenticate", jwtUtil, userDetailsService, authenticationManager()), UsernamePasswordAuthenticationFilter.class);
http.addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);
```

That's all it takes to set up a token auth scheme. Make sure to check out the pull request for a few tweaks needed to make everything work properly.

## Updating Book

The last thing we need to do is assoicate each `Book` record with a `User` so that users can keep track of their books and their notes. Most of this is pretty straightforward, but be sure to check the pull request for the changes if you're coding along, particularly to the `Book` entity as well as the `BookController` and `BookService`.  


## Wrap Up

Now Booktrackr has authentication and security. You should be sure to take a look at the pull request for this post, because there were a number of smaller changes not demonstrated above. Feel free to send me an email, leave a comment, or comment on the PR if you have any questions about what each change is doing.

That should just about wrap up the API and back end portion of the application. There were almost certainly be changes required in conjunction with building out the web and mobile clients, but I'll highlight those as we go.

### Resources

* [Pull Request](https://github.com/rpmartz/booktrackr/pull/10)
* [JSON Web Tokens](https://jwt.io) 
