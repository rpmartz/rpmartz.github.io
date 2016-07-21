---
layout: post
title: Booktrackr Web Client V - Building Out the Web Client 
date: 2016-07-21
comments: true
published: true
---

Now that we've built sign up and login workflows, we can build out the rest of the basic functionality of the Booktrackr's web client. 

There's not a whole lot of anything new here. Most of the AngularJS features that Booktrackr uses are very common. One thing that is worth noting is that the application now uses two third party directives: one for paging through books and one for rendering notes from Markdown to HTML. 

### Third Party Directives

#### UI Bootstrap Pager

[Bootstrap](https://getbootstrap.com/) is one of the most popular CSS frameworks for building responsive web applications. It has tons of components that render well on devices of all sizes and takes care of handling basic animations for you. The problem is that Bootstrap's Javascript doesn't always play nicely out of the box with AngularJS. That's the bad news. The good news is that the Angular team has implemented the most popular Bootstrap components as AngularJS directives in a project called [UI Bootstrap](https://angular-ui.github.io/bootstrap/). 

We just need to add this markup:

```html
 <uib-pagination total-items="books.totalBooks" ng-model="books.currentPage" items-per-page="5"
                        ng-change="books.updatePage(books.currentPage)"></uib-pagination>
```

to the `books.html` partial and a little bit of paging logic to the `BooksController` in `controllers.js` and we can page through the books, which is a little bit better user experience.  

#### Markdown Directive

The other third party directive we're going to use is a markdown rendering directive written by [Brian Ford](http://briantford.com/). This one's pretty simple; it just renders the notes, which are stored as plain-text Markdown, into HMTL for the "View Book" screen. Here's what the directive looks like in the `view-book.html` partial:

```html
<div class="well" btf-markdown="book.book.notes">
```

Note that both directives need to be injected into the application, and you can see that in `app.js`. Don't forget to add these as `bower.json` dependencies. Check the pull request diff for this post if you're confused. 

### Build Improvement

The other thing we're going to do in this pull request is improve our front end build process with Gulp. So far, we've used Gulp plugins to automatically inject both our custom front end assets and third party dependencies in the `bower.json` file. That's great, but as those of you following along and building this as we go, you've noticed we have a good number of Javascript files now. 

In reality, most of our Javascript files are pretty small, so in practice this isn't going to make a huge difference, but the fewer network requests for external assets the browser needs to make to render a page, the better. To help accommodate this, we can use the [Gulp Useref](https://www.npmjs.com/package/gulp-useref) plugin to consolidate all of our third-party Javascript files into a single `vendor.js` file and all of our custom Javascript files into a single `booktrackr.js` file. 

## Wrap Up

So now we have a (mostly) complete web application to keep track of books that we've read and write notes on them. There are still a few rough spots to fix, and we have to make some tests for the front end to make it easier to maintain, but the lion's share of the work for the front end is complete. 

Next time, we'll write tests for our Javascript.

### Resources

* [Pull Request](https://github.com/rpmartz/booktrackr/pull/19)
* [UI Bootstrap](https://angular-ui.github.io/bootstrap/)
* [Markdown directive](https://github.com/btford/angular-markdown-directive)
* [Gulp Useref plugin](https://www.npmjs.com/package/gulp-useref)

