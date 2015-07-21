---
layout: post
title: Popularity of Strongly Typed Languages with Type Inference Will Grow
comments: true
published: false
---

So you just got hired as a brand new developer...now what? As someone who was in that position in the not-so-distant future, I think I have a few thoughts that new developers may find beneficial. 

I've been to paid to write mostly Java, but this advice is for the most part programming-language agnostic and should apply well to all the modern general-purpose programming languages. 

## Above All, Maintain Perspective

Before we dive in, a word about the technology industry. It moves quickly and things are always changing. While many of the shiny new breakthroughs are really just old ideas repackaged with snazzy new marketing, the reality is that new languages, frameworks, and techniques are constantly surfacing. Sure, you can "do the same with Java," but the reality is that if you're going to have career longevity in this industry you will need to approach your craft as a practice and make time to keep abreast of industry trends, both from a technical perspective as well as from a business perspective.

With that said, don't get caught on the treadmill. It's impossible to know everything, nor is it a good use of time to try to do so. The best paradigm I've found through which to view this is from the _[Pragmatic Programmer](https://pragprog.com/book/tpp/the-pragmatic-programmer)_: treat your technical knowledge like an investment portfolio. It's ok to speculate a little bit of your money trying to pick the next Microsoft or Google, but the bulk of your portfolio should be put into stable, unsexy investments like blue chip stocks or mutual funds. Similarly, it's fine to spend some time toying around with this week's latest Javascript single page MVC framework, but spend the bulk of your time learning the fundamentals that will survive the constant churn of programming languages and frameworks. Peter Norvig has a great take on this [here]().

And have a life...don't spend all your time glued to a computer.

## Your First Year

Here's what I think a new developer ought to do in his or her first year. One can certainly learn all these things faster given discipline, dedication, and free time, but for someone with a life and a day job I think a year is enough time to do each area justice without dedicating every available minute to this.

Other than the first three, I don't think the order of these things matter too much, so pick what's relevant and interesting given what you're currently working on.

### Be Systematic

Establish a system to capture all of the material this process will have you learning. It doesn't need to be fancy...[Markdown]() files in Dropbox or something like [Evernote]() will suffice. Periodically re-read your notes to make sure those lessons are not forgotten. 

When you come across a problem, take time to actually understand the solution and make a few notes about it. New developers are often in a hurry to be productive and demonstrate their productivity, but take some time to learn what's going on and in the long run you'll be more productive for it. So no, that means no copy and pasting Stack Overflow answers until something appears to work, take the time to understand the problem as well as the solution.

### Learn the Fundamentals 

Regardless of what language you're learning, take time to learn the syntax and standard libraries. Make sure you are rock solid on things like language syntax, keywords, access modifieers, inheritance, and your language's type system. 

I think the best place to start for this is your language documentation. Java has a great [tutorial series]() that takes you through all the major "paths" of the language. You can find your language's version of this and should spend time reading through it and understanding it.

Once you're up to snuff on the basics of your language, you should spend some time reading good code that more experienced programmers have written. If you work for a company, the best choice for this may well be in your own code base. I had the opportunity to work with someone who worked for Sun on Java, and reading the code he wrote was extremely informative. The added benefit of reading code from your company's own codebase is that you can help get up to speed on company norms like code formatting, comments, etc. Another good option here are popular open source libraries. Bonus points for ones you use. Just get hired as a Python web developer? Read through some of the [Flask]() or [Django]() source code. Make some notes about what you don't understand and go find the answers. At first, it won't seem helpful, but you'll absorb so much knowledge during this process that one day it will hit you how much more you know than just a few months ago.

Spend some time reading through the documentation of the standard libraries as well. Chances are if you need your code to do something, many developers before you have as well and there is either a solution included in the language itself or in a widely-used, well-tested, optimized third party library.

- fundamentals of the syntax of your language
    + read the language docs
    + find some good code to read...start with the frameworks you use

### Use Version Control

This one should go without saying, but if you don't know how to use version control to manage your source code, that should be the first thing you learn. Your future self will thank you. 

If you are on a project or work for a company that does not use source control, just know that's wrong as can be and asking for trouble. 

There are plenty of good tutorials out there...GitHub and Bitbucket both have good intro articles, and there are excellent free books on how to use [Git]() and [Subversion]().

- version control

### Testing

- learn how to test in your language and integrate testing into your workflow

A lot has been written of what to test, how to test it, and how much you should be testing. Maybe some day in the future all developers will decide on the optimal level of test coverage in a code base, but I'm not holding my breath. What no sane developer debates, however, is that you should be testing your code with automated tests that you run often. 

Learn the basic concepts behind [Test Driven Development](). Writing your tests first is not a panacea, but it forces you to think about what the code needs to do, the conditions it should handle and how it should handle them, and how to structure it so it is modular and testable. In theory, if you can anticipate all of the requirements and all of the possible inputs, then write good test cases for those conditions, once you write your code and all your test cases pass, then you know your code is correct. In practice, this is either impossible or impractical, but it's a good goal to strive for and will almost certainly lead to better code.

Some languages have testing libraries built in, like Python's `unittest` module. Other languages have 3rd-party libraries that are widely used for testing like [JUnit]() for Java. All languages have ways to simplify mocking interactions with other components so that you can test different conditions. Learn how to use these libraries and integrate testing into your workflow. You should be writing tests regularly and running your test suite many times throughout the course of a normal work day.


### Build and Dependency Management

- learn how to build software and manage dependencies


- learn the basics of relational databases
- learn how to debug
- find some timeless, authoritative books to help you write better code (clean code, code complete 2, refactoring, etc.)

## Some 