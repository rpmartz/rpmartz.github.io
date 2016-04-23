---
layout: post
title: DynamoDB Concepts In Practice
comments: true
published: false
---

Welcome to Part III of my DynamoDB series. If you missed them, you should check out [Part I](http://ryanpmartz.com/dynamo-db-part-1) that gave a high level overview of DynamoDB and [Part II](http://ryanpmartz.com/dynamo-db-part-2) that dug into some of the concepts you need to understand in order to model your DynamoDB objects.

Before we dive in, I want to clarify that I'm not really writing this as a "how to get started with DynamoDB" for absolute beginners. I'm assuming that you can follow [Amazon's DynamoDB Documentation](https://aws.amazon.com/dynamodb/getting-started/) to set up a table and know how to work with AWS in your language of choice. These posts are meant for someone new to key-value stores or DynamoDB to help build their data model and use DynamoDB appropriately.

## Our Sample Application

Somewhere in DynamoDB's docs there's an example of using DynamoDB to save scores for games. Let's say that we're running a website that users visit to play games, and we've decided to use DynamoDB to record the scores of those games. To start with, here's what attributes our records will have:

* `User ID`
* `Timestamp`
* `Game ID`
* `Score`

### Sample Domain Classes

We'll use Java and Python examples for the rest of this series.

#### Our Java Domain Object
