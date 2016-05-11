---
layout: post
title: Booktrackr Part IX - Adding a User Model
excerpt: Adding a `User` domain object
date: 2016-05-10
comments: true
published: false
---

The "back end" of Booktrackr is almost built out. We've got a `Book` entities and corresponding CRUD REST endpoints with unit and integration tests for that. Now we need to add users so that users can save books and notes and retrieve them upon returning to the site.  

## Extracting a `BaseEntity`

In preparing this post I realized I had omitted some important fields from our `Book` entity. Hibernate and JPA give you the ability to add `created` and `modified` timestamps to an entity, and works a lot better if you provide a `version` field for each entity. You can read about how the `version` field is used for optimistic locking in the Hibernate docs, but suffice it to say we want these fields for all of our entities.

Even though we want these on all of our entities, we don't want to make a bunch of different `@Version` fields, so we're going to extract these fields into a class called `BaseEntity` and then let our entities inherit from it:

```java
@MappedSuperclass
public class BaseEntity implements Serializable {

    private static final long serialVersionUID = 1L;

    @GeneratedValue(generator = "uuid2")
    @GenericGenerator(name = "uuid2", strategy = "uuid2")
    @Id
    @Type(type = "pg-uuid")
    protected UUID id;

    @CreationTimestamp
    private Date created;

    @UpdateTimestamp
    private Date modified;

    @Version
    private Integer version;

    public UUID getId() {
        return id;
    }

    public void setId(UUID id) {
        this.id = id;
    }

    public Date getCreated() {
        return created;
    }

    public void setCreated(Date created) {
        this.created = created;
    }

    public Date getModified() {
        return modified;
    }

    public void setModified(Date modified) {
        this.modified = modified;
    }

    public Integer getVersion() {
        return version;
    }

    public void setVersion(Integer version) {
        this.version = version;
    }
}
```

I don't foresee Booktracker using dates extensively, but if you plan to use dates you'll want to check out the Hibernate Java 8 library that allows you to use Hibernate to use Java 8's `LocalDate` instead of rather unpleasant pre-Java 8 `Date` class.

You'll also want to make sure that you modify `Book` to `extend` `BaseEntity`, and add a migration to add the required fields to the `book` table:

```sql
ALTER TABLE book ADD COLUMN created TIMESTAMP NOT NULL DEFAULT NOW();
ALTER TABLE book ADD COLUMN modified TIMESTAMP NOT NULL DEFAULT NOW();
ALTER TABLE book ADD COLUMN version INTEGER NOT NULL DEFAULT 0;
```

## Adding a `User`

Now we can add our `User` object:

```java
@Entity
@Table(name = "users")
public class User extends BaseEntity {

    private static final long serialVersionUID = 1L;

    @Column(unique = true)
    @Email
    private String email;

    @Column
    private boolean enabled;

    @Column(name = "first_name")
    @NotBlank(message = "User first name may not be empty")
    private String firstName;

    @Column(name = "last_name")
    @NotBlank(message = "User last name may not be empty")
    private String lastName;

    @Column
    private String password;

    // getters and setters
```

Pretty straightforward class there. The `@Email` and `@NotBlank` annotations are for validation of the objects when creating them in the controllers, which we'll get into later on in the series.

This migration is a little bit simpler than the `book` migration above since it's a `CREATE TABLE` migration:

```sql
CREATE TABLE users
(
  id         UUID PRIMARY KEY NOT NULL,
  created    TIMESTAMP        NOT NULL,
  modified   TIMESTAMP        NOT NULL,
  version    INTEGER          NOT NULL,
  email      VARCHAR(255),
  enabled    BOOLEAN          NOT NULL DEFAULT FALSE,
  first_name VARCHAR(255),
  last_name  VARCHAR(255),
  password   VARCHAR(255)
);

CREATE UNIQUE INDEX uk_email ON users (email);
```

And that's all it takes. That's our `User` domain object.


## Wrap Up

In this episode, we used inheritance to pull up some common fields on our domain entities into a superclass, which required some modifications to our `Book` entity. We also added a `User` domain entity.

Next time we'll implement the create account endpoints and tests.

### Resources
