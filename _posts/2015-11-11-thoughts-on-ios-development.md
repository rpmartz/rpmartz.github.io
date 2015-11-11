---
layout: post
title: Thoughts and Observations on iOS Development
comments: true
published: false
---

My first iOS app, [Amatracker](https://github.com/rpmartz/amatracker), is currently waiting for review in the app store. It's nothing fancy; just an app version of the paper workout trackers we use at [CrossFit Amatak](http://crossfitamatak.com). Hopefully I can add a link to it in the App Store soon, but in the mean time I thought it would be a good time to write down a few observations about the process. Bear in mind these are coming from the perspective of a developer who writes Java for his day job.

## Resources

### Bitfountain

I wrote Amatracker using [Swift](). Most of what I needed to learn, I used [Bitfountain](http://bitfountain.io)'s iOS 8 course for. They have since stopped selling enrollment to that particular course, and moved to a subscription model, but they're still great courses. Fair warning, they aim to be accessible to new programmers, so they can sometimes be redundant or slow if you're an experienced programmer, but they're a great introduction to iOS development. The subscription model also gives you access to their iOS design courses, which is a nice bonus.

### Hacking With Swift

Recently, I've started working through the [Hacking with Swift](https://hackingwithswift.com) tutorials by Paul Hudson, which are excellent. Personally, I like them a little bit better than the Bitfountain videos. I can skim over or skip the passages that I don't need, and focus on the things that are new. He also includes introduces the author to a lot of XCode shortcuts that have been helpful. The best part is that _Hacking with Swift_ is **free**, but if you're able I'd encourage you to support the author on [Gumroad](https://gumroad.com/l/hws-book-pack).

## Swift

This was a pretty a pretty basic app, so I don't want to give off the impression that I've pushed Swift to its limits, but from what I've seen so far I really like Swift. 

Type inference just works and keeps code less cluttered. Writing Swift feels more like writing Python than Java, but with the benefit of the compiler smacking you upside the head when you get something wrong and strong runtime performance. 

I hate `null`/`nil` and really like that Swift's optionals can force the programmer to deal with those values at compile time. Real closures and default method/function argument values are nice as well. 

Swift's functions do have a learning curve with their internal and external argument names, but they're not so bad once you get used to them. [This article](https://www.objc.io/issues/16-swift/swift-functions/) is a good resource to really dive into how Swift functions work.

## XCode

Java developers are spoiled with respect to tooling. At first, I was not very impressed with XCode. I've come to appreciate it a little bit more as I've learned more about it, but still think it leaves a lot to be desired. 

The interface builder is the best part, in my opinion. It's fluid, easy to use, and (mostly) bug free. The dusk theme is a gorgeous color scheme. If you're going to look at lines of code for hours on end, it's important that the theme not strain the eyes and be nice to look it. 

A lot of the capabilities around editing could be improved, though. There's no default shortcut to delete an entire line, for example. The refactoring tools are extremely limited, and not all of them even work for Swift yet. As I write this, XCode does not support a "Rename Variable" refactoring for Swift variables. Some of that my be due to how new Swift is and be better supported in Objective C, to be fair, but the current state is lacking. As I learn more iOS and get more comfortable building layouts programmatiacly, I'll definitely be looking into JetBrains's [AppCode](https://www.jetbrains.com/objc/) as an alternative editor for iOS development. 

So those are some initial observations after producing my first app. Let's hope it gets approved, and it will be interesting to look back on these and see how valid they remain as I learn more of the iOS ecosystem.

