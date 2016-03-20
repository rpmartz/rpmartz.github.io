---
layout: post
title: Introduction to Amazon's Dynamo DB
comments: true
published: false
---

Unless you've been living under a rock for the past 5 years, it's no big secret that NoSQL databases can be a good choice for a certain class of data storage solutions. In this series of blog posts, we'll take a look at Amazon Web Services's NoSQL database, DynamoDB.

## What is Dynamo DB?

The [AWS DynamoDB Product Page](https://aws.amazon.com/dynamodb/) states that

> Amazon DynamoDB is a fast and flexible NoSQL database service for all applications that need consistent, single-digit millisecond latency at any scale. It is a fully managed cloud database and supports both document and key-value store models.

Sounds awesome, doesn't it? That's because AWS is trying to sell it to you! Sarcasm aside, let's take a look at a few of the keywords in that description.

* _fast_: DynamoDB is designed to be fast and responsive even over very large data sets. "Single digit millisecond latency at any scale" takes a little bit of work from you in terms of understanding the data model and architecting your application appropriately, but the smart folks at AWS have used their hard won knowledge running Amazon.com and solved a lot of hard problems like spreading your data over multiple machines and using SSDs to keep queries and writes to DynamoDB fast. This lets you start small and pay only for what you need without worrying about whether your database scales.

* _fully managed cloud database_: Another way to put look at this is (a NoSQL) database-as-a-service. You focus on developing your application (and making sure you pay your AWS bill every month) and AWS takes care of the management, capacity provisioning, scaling, backups, etc. when it comes to the database. No servers to image or backup, no concerns about hardware failure, no worries about whether to scale up by buying bigger hardware or scale out by sharing nothing - AWS handles all that and you just pay the bill and focus on your application.

* _flexible_: Anyone who's worked on applications backed by a relational database (RDBMS) knows that they're great. Relational data models and database engines are well understood and have endured the test of time in a way that perhaps no other technology has. But they're not without their costs, both in terms of ease of development and on scalability. Relational databases [have shown they can scale quite well](https://gigaom.com/2011/12/06/facebook-shares-some-secrets-on-making-mysql-scale/), but developers like the schema-less flexibility and its positive impact on development times that NoSQL offers. Specifically in DynamoDB's case, you are required to have a hash key but other than that there is no schema enforcement.

* _supports both document and key-value store models_:

## Who should use DynamoDB?

We'll talk a little bit more about use cases after we explore DynamoDB in depth, but based on the considerations outlined above, here are some potential use cases that might be a good fit for DynamoDB:

* Small teams who want or need a key-value or document store but don't want to be server or database administrators.

* Teams that know their data will require a scalable persistence solution due to very high read or write capacity requirements, and don't have the capability or desire to manage those databases and capacity in house. Think sensor data with multiple writes per second from each sensor.

* Storing web app sessions in DynamoDB. With single-digit query latency, DynamoDB is probably approaching what you'd expect from an in-memory cache like Memcached or Redis from a performance perspective. Using DynamoDB to store sessions can ease some of the burden of scaling your application horizontally across multiple app servers without having to worry about session affinity.

## Who should _not_ use DynamoDB?

Everything above is fine and dandy, but what sorts of problems is DynamoDB _not_ a good fit for?

* Classes of problems that need true ACID transactional semantics, like a banking application.

* Applications whose authors or owners cannot or do not want their data in the cloud, or are not comfortable making that level of commitment  to a 3rd-party-provided service. The cloud has proven its worth and the benefits far outweigh the risks for most applications, but there are still some apps and problems out there that fundamentally shouldn't be stored on other peoples' servers.

## Conclusion and future posts

Hopefully you have a general overview of what DynamoDB is and some broad guidelines for what sorts of applications it can be good for. In Part II we'll take a look at some fundamental concepts developers need to understand to use DynamoDB effectively. 
