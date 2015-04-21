---
layout: post
title: Popularity of Strongly Typed Languages with Type Inference Will Grow
comments: true
published: false
---

It's hard to separate the signal from the noise in the tech industry. Shiny new toys show up every day promising silver bullets and one-size-fits-all solutions that are neither. Just look at the [current state of JavaScript frameworks](http://todomvc.com/) as an example. One trend that I think will continue to gain popularity, though, is the use of staticly-typed languages with type inference. I don't mean to argue they'll take over...no one language or ecosystem ever will...but I think that they will continue to gain developer mindshare by hitting a nice balance between the productivity and expressivity of scripting languages like Python or Ruby and the additional help that strong types and a compiler provides.

Perhaps the single strongest example in favor of my argument is Apple's new programming language [Swift](https://developer.apple.com/swift/). It's statically typed and melds object oriented and functional programming concepts. Swift is already an [overnight success](http://thenextweb.com/insider/2015/01/15/apples-swift-exploded-popularity-2014-javascript-still-popular-programming-language-overall/) and will continue to grow in popularity due to the ubiquity of Apple mobile devices. [Scala](http://www.scala-lang.org/) is another multi-paradigm language with type inference. It's popularity lags behind the mainstream general purpose languages, but it's gaining traction. Haskell and OCaml are two other contenders, but I doubt they'll ever approach mainstream popularity. 

Using a language like Swift approaches the best of both worlds. Code is shorter, easier to read, yet still checked at compile time. Mangle a variable name and the compiler will stop you in your tracks. Throw in a preference for immutability baked into the language and there's not much to dislike. No, a compiler cannot catch all bugs, but it's more or less free help, with perhaps a slightly longer development time due to recompiling. I'd argue that any additional development cost(s) are more than balanced out by the tooling, especially as the number of lines of code grows. 

No one's going to be porting large, stable Java codebases to Scala just because Scala's a more developer-friendly language, but my bet is that more and more new code will be written using languages with better features than most developers are stuck with currently. 