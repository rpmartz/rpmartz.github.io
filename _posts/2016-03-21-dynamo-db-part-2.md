---
layout: post
title: Fundamental Concepts Underlying DynamoDB
comments: true
published: false
---

This post, part II of the DynamoDB series, will talk about what you need to understand in order to use DynamoDB effectively. Like I mentioned in [Part I](http://ryanpmartz.com/dynamo-db-part-1), you have some work to do in developing your application in such a way that you are able to use DynamoDB effectively.

## Hash Tables

In order to understand DynamoDB and use it effectively, developers have to understand the idea of a [hash table](https://en.wikipedia.org/wiki/Hash_table). At a conceptual level, if you squint hard enough, DynamoDB is just a giant hash table that the smart folks of Amazon have distributed across machines and stored on SSD to make it _really_ stinking fast.  

### A what table?

A hash table is a data structure that maps keys to values with constant time insertion and lookup operations. (That's `O(1)` insertion, search, and deletion for all you algorithms nerds out there.)

A lot has been written about hash tables in terms of computer science research papers and textbook, but the so what for you is that it's a key-value lookup data structure. Concretely, that means that given a list of names, searching whether the name `"John Smith"` is in that list will take the same amount of time whether the list is 10 names long or 10 million names long.

You can read more about hash tables elsewhere, and I recommend that you do since they're so fundamental, but let's keep this discussion concrete and look at these concepts in practice.

## Hash Tables for the Working programmer

You may or may not have been awake when your algorithms and data structures teacher said that hash tables are fundamental to all programs, but in either case your teacher was right. Let's say you program in Java. The good news is that even though hash tables are a theoretical computer science topic, they're also widely used in practice and you're more familiar with them then you realize.

### Hash Tables in Java

I see you over there in your business casual outfit, Enterprise Java Guy.

If you've ever used a `HashMap` in Java, you've used a hash table, and if you understand how to work with `HashMap`s in Java then you'll have an intuitive grasp of DynamoDB's query model. Suppose we're building an address book for our friends and are using a `HashMap<String, Address>` to store our friends' addresses. If we have a friend named `"Jane Doe"`, then we know that we can easily check whether Jane's address is in our address book using the `containsKey()` method:

```java
if(addressMap.containsKey("Jane Doe")) {
  // do something
}
```

Similarly, we can update or remove Jane from our address book using the `put()` and `remove()` methods. Underneath the hood, Java's using the hash code of the map keys to map those keys to `Address` values.

#### Constant Time operations

Whether we have 10 or 10,000 or 10 million addresses in our address book, it takes the same amount of time to compute a hash key for a given friend's name to check if the key has an associated value, save an update, or remove the `Address` from the hash table. Fancy computer science types call these "constant time" operations since the time to perform the operation does not increase as the number of elements in the data structure increases.

### Supporting Queries

We saw above that a hash table provides fast lookups when you can search using the key. But what happens if we, for example, are traveling and want to find all of our friends who live in California? As it stands, our `HashMap<String, Address>` does not support this type of query very well. We'd have to do something like:

```java
List<String> friendsInCalifornia = new ArrayList<>();
for(String friend : addressMap.keySet()) {
  Address address = addressMap.get(friend);
  if("California".equals(address.getState())) {
    friendsInCalifornia.add(friend);
  }
}
```

The important thing to notice here is that we now have to look through every `Address` in our address book to find all of our friends who live in California. If our address book is small, then this is not a very big deal. But as our address book grows, it will take longer and longer to answer questions like this. The fancy computer science term for queries like this that have to look over the entire data set "linear time" or `O(n)` operations, where `n` is the number of elements in the data set. For simplicity, pretend it takes 1 second to check each friend's address to see if it's in California, and we have 10 friends in our address book. That means it will take 10 seconds to find all of our friends who live in California. If we make more friends and now have 20 friends in our address book, it will now take 20 seconds to find all of the entries in our address book.

## Back to Dynamo Already

Ok, enough fancy terms. What's any of that got to do with DynamoDB? Well, way back in Part I, I said that you should think of DynamoDB as a giant hash table. Hopefully, even if you haven't studied hash tables before, you can see from the simple examples above that when you work with hash tables you want to do everything you can to perform all of your lookups using keys that map to values, and avoid doing things that require you to look at every value in your hash table.

In the language of DynamoDB, that means you want to use _query_ operations whenever possible as opposed to _scan_ operations. A DynamoDB _query_ is fast and efficient; it's a lookup by key, so it only loads and returns values that match that key. In contrast, a _scan_ operation looks over every element of the table. It can still be pretty fast since DynamoDB is optimized for fast reads, but it's very inefficient compared to a _query_.

We'll dive into the details in the next post to make this concrete. The big takeaway from this post is that when designing your data model for DynamoDB, you have to think about the types of queries you'll need to do and design your tables accordingly. Stay tuned for Part III.
