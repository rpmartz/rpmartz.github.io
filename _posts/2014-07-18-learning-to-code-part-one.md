---
layout: post
title: Learning To Code&#58; Part One
comments: true
published: true
---

This is part one of a series of posts aimed at people who have made the decision to learn how to code. I'm not claiming to have all the answers, but as someone who recently transitioned from being an infantry officer to a final semester graduate student in computer science and software engineer at a startup, I believe I have some perspective to offer. I don't envision the series being a step-by-step "How To" as much as it is a distillation of things I wish someone had told me at the beginning of my journey. Take it for what it's worth, and like anything in life, your mileage may vary.

First, a quick detour for the obligatory pep talk. Learning to program can be a frustrating experience. Sometimes it seems like an insurmountable task just to get a development environment set up and decipher cryptic error messages. Experienced engineers don't realize how much they've absorbed over time and it can be hard to remember how overwhelming everything seems at first. Don't get overwhelmed; everyone was a beginner once. Stay tuned for another post in this series on where to turn for help deciphering those cryptic error messages, but in the mean time hang in there and try to look at them as learning opportunities.

The first order of business is to choose a programming language to learn. There's plenty of opinions about the "best first programming language to learn" and no single correct answer. Some choices, however, are better than others. Read on for a quick overview of the candidates and my recommendations.

## Language Choices

Unless you pick some very obscure, outdated language that is not used presently in industry, it's hard to really go wrong with any mainstream, popular programming language. If someone you know is willing to mentor you along the way, ask him or her for a recommendation and follow it. Once you learn a programming language, learning a second one is much easier.

Ok, let's dig in.

### Good Choices

#### JavaScript

Until recently, [JavaScript](http://en.wikipedia.org/wiki/JavaScript) was a language used in web pages to make them interactive. It is often referred to as a "client-side" programming language, meaning it ran in the browser of the client, or person accessing a web server, as opposed to on the web server itself. Recently, with the advent of [node.js](http://nodejs.org), JavaScript has been liberated from the browser and it's possible to write command line and server-side programs in JavaScript.

JavaScript is enjoying a bit of a boom right now, with an explosion of different front-end frameworks to choose from. There's no doubt that it's a popular, useful language. If you plan do to any sort of web development at all, you will need to know JavaScript. The downside, in my opinion, is that you cannot do too much with plain JavaScript. You need to learn libraries like [jQuery](http://jquery.com), which appears very simple, but hides a lot of the implementation and complexity from the programmer. This is a nice productivity feature, but is not an ideal characteristic for someone trying to learn.  

#### Ruby

Ruby is a scripting language that has seen huge popularity thanks to [David Heinemeier Hanson's](http://david.heinemeierhansson.com/) [Rails](http://rubyonrails.org/) web application framework. In fact, despite the fact that Ruby is a fully-featured, general purpose programming language, for the most part when you hear about Ruby or Rails people are referring to Ruby on Rails.

Ruby is a well-designed, simple, and elegant language that incorporates interesting features from languages that preceded it. Rails is an extremely popular web application framework, and is not a bad choice for someone looking to learn a language that will make him or her marketable.

The problem with Ruby as a first language, from my perspective, is that it's predominately used with Rails as a web application framework. If you know for a fact that you want to be a web developer, Ruby on Rails is a solid first choice. If you're not quite sure what you want to do with your newfangled programming skills, you might want to pass on Ruby since it's predominately a web application tool.

#### C&#35;

[C#](http://en.wikipedia.org/wiki/C_Sharp_(programming_language)) is a strongly typed, object-oriented language developed by Microsoft in response to Java. It's a powerful language that has been used to develop a lot of enterprise software. Learning C# will teach principles of object-oriented programming, compilation, garbage collection, and a static type system, which are important concepts despite what some scripting language fanboys would have you believe.

The only downside to C# is that it restricts you to Microsoft platforms. While this is not a _de facto_ downside, it does limit your flexibility/marketability when compared to a truly cross-platform language.

#### C/C++

C and C++ are what operating systems and games, respectively, are written in. They are the oldest languages on this list and still in widespread use. Learning either of these languages will yield a lot of understanding about the underlying architecture of the computer, how memory is allocated and deallocated, etc. They are also still the most performant programming languages used today.

Despite their popularity, I don't think that either of them are good choices of first languages to learn, unless your goal is to be a computer game programmer, in which case C++ might make sense. Both of them have platform-specific compilers that can be complicated to get working correctly. Both of them require manual allocation and deallocation of memory, which is error-prone. They are worth adding to your toolkit down the road, but most people should pass on them as first programming languages.

#### Objective C or Swift

I don't know much about [Swift](https://developer.apple.com/swift/), as Apple just unveiled it, but it and Objective C are used to build iOS applications on iPod, iPhone, iPad, and Mac OS X. They're also in demand in the workforce, since everything is moving mobile. Objective C is not an easy language to learn, but iOS apps are very popular and an app is a very tangible thing that can keep first time programmers motivated.

### Better Choices

#### Java

Besides C and C++, Java is the most widely-used programming language out of the mainstream languages. It probably runs more enterprise software than any other language on this list, and isn't going anywhere anytime soon. It's also the language that Android applications are written in. Some criticize that Java is overly complicated and verbose compared to other languages, which may be fair criticism, but I think that Java strikes a good balance between low-level enough to learn a lot about what is actually going on in a program while still being high-level enough to be productive without worrying about things like memory allocation. There are also a very, very large amount of Java frameworks and libraries for doing everything from building secure web applications to doing statistical processing and machine learning. Java can run on Windows, Linux, and Mac, so learning Java won't limit you to one operating system or runtime.

#### Python

Python is a dynamically-typed scripting language that runs on all mainstream operating systems. It has a very simple, elegant syntax, and makes it easy to write powerful programs quickly. It is widely used, and just [surpassed Java as the top introductory programming language in top computer science university programs](http://www.javaworld.com/article/2452940/learn-java/python-bumps-off-java-as-top-learning-language.html). Just like Java, there are excellent frameworks for building web applications, processing Big Data, or working with scientific data. Learning Python will provide you with a powerful tool to write programs and applications in a lot of different domains.

## Conclusion

If you still can't decide, learn Python. It's simple, powerful, and flexible enough that you'll be able to do a wide variety of things with it. It's also popular in the web development, scientific, and Big Data fields, so it'll give you some good career skills.

Learning to program is a smart move, whether your goal is to exercise your logic skills, make a career change, or just automate some boring tasks. Maybe you want to build statistical models for your fantasy football team, or maybe you want to automate sifting through the spreadsheets that the jerk in accounting asks you to review each week. Whatever your goals, I think the return is well worth the time and energy required to learn it.

Stay tuned for part two of the series, which will cover some resources for people learning to program.
