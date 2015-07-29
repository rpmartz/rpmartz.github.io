---
layout: post
title: A Bare Bones Introduction to Python for Java Developers
comments: true
published: false
---

Every developer knows that he or she ought to be learning new languages from time to time. And it's true that for the most part, once you know one language, learning the syntax of another one is fairly straightforward. But the barrier to being productive in the new language and it's associated ecosystem can be substantially higher. How do you manage dependencies? What files should or should not be checked into source control? What tools do most developers use to be productive? 

The goal of this post is to provide a (very) brief, high level overview of Python for the experienced Java developer. It is not a Python vs. Java article. It is not a "how to" article or why I think one tool is better than the other. It's just a short article to help someone coming from Java get productive in the Python ecosystem faster. I'm assuming that providing links to documentation is enough to get an experienced developer started. If not, "How Tos" are a short Google search away. 

## Code Editor

First, a word on IDEs...Java developers love them an IDE, that's for sure. I can't imagine writing Java without one. And in my opinion, trying to write Java without one is foolish; the benefit you get in exchange for RAM consumption is a no-brainer. Although Python is simple enough that you may be able to get away with vim or a text editor like Sublime Text, but if it's a Python IDE you seek, then look no further then JetBrains' awsome Python IDE [Pycharm](https://www.jetbrains.com/pycharm/). IntelliJ users will feel right at home, and it's not that steep of a learning curve from other Java IDEs. In my opinion, if you're just writing small programs or scripts with Python, then an editor more than suffices. For large things like web development or making heavy use of Python libraries like NumPy for data crunching, an IDE might be worth it.

## Dependency and Library Management

Most Java projects use a build tool/depenency manager, such as Maven or Gradle. Since Python is interpreted, you don't need a build tool _per se_, but there's a whole lot of open source library goodness going on in the Python world that you'll want to take advantage of.

[Pip](https://github.com/pypa/pip) is a popular Python dependency management tool. There are others, like `easy_install`, but `pip` comes bundled with newer versions of Python.

### Environment Isolation

The only issue with `pip` is that by default it installs pacakges system wide. If you have multiple projects that use different versions of the same module, this can be problematic. One solution is `virtualenv`. `virtualenv` allows you to install dependencies, or even different versions of Python, and keep them isolated from one another with only the third party dependencies needed for the specific project installed. 


## Unit Tests

### Mocks


### Running Them