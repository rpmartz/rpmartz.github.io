---
layout: post
title: Tips to Make Computer Science Education More Practical
comments: true
published: true
---

>  Beware of bugs in the above code; I have only proved it correct, not tried it.
> [Donald Knuth](http://cs.stanford.edu/~uno/)

## Theory vs. Practice


The debate about the right balance of theoretical instruction and practical experience in formal computer science education won't be settled anytime soon.

By "formal computer science" I mean most university computer science courses focused on more academic or research focused areas. Some areas that might fit into this category include category theory, algorithmic analysis, number theory, linear algebra, mathematical proofs, UML, and just about anything else that one could write a masters or PhD thesis on.

By "practical experience" I mean things that employers care about. Skills that universities, in my estimation, have figured people would learn on the job. Things that land in this bucket might be version control, system administration, testing, refactoring, documentation, or evaluating new frameworks.

To be clear, I'm not saying that there's no value in learning the "academic" portions. The foundations are important, and any professional software engineer should be grounded in them. And for many niches they are necessary; good luck being a competent machine learning engineer without a grasp of linear algebra and statistics. Likewise with computer organization and architecture if you're a malware analyst. 

It's not that there's no value to the academic skills or that they're unnessary. This is not a "theory is uselss post." My argument is that most people studying computer science are doing so to get a job. Maybe they're not set on a career as a professional software engineer, but they're almost certainly looking for a paycheck for a few years to whittle down the mountain of debt they're likely incurring.

So, with that in mind, here are some things I think instructors ought to consider incorporating into coursework and projects in order to give students some exposure to and experience with skills that employers will require.

## Version Control

Every major project should require the use of version control. By the time students graduate, they should have had to have used version control on a non-trivial multi-member project. The first merge conflict someone faces should not be in week 1 of his or her first job.

Git is the obvious leader in terms of open source version control systems but Subversion or Mercurial are equally good choices. Which system used matters a lot less than just making students learn and use version control.

Bonus points for distributing and accepting assignments via the version control system. For example, many teachers provide starter code for projects. Instead of emailing this to everyone or hanging it somewhere for students to download, require that they fork it from the school's git repository and then send a pull request to turn it in. 

## Documentation

Every project should have extensive documentation. That includes code documentation like function/method documentation as well as "higher level" documentation like what problem(s) the software is solving, how to check it out and build it, etc. The litmus test for this should be whether someone unfamiliar with a project can check it out on a clean machine, build it, and use it following only what is in the project documentation.

As an aside, the usability and appearance of the documentation should be considered as part of the grade. Tools like Markdown, Javadoc, or Sphinx make it very easy to produce high-quality, usable documentation. 

## Testing

At some point, perhaps from the beginning of junior year onward, every project should be required to have at least unit tests. In a perfect world, test coverage would be close to 100% and there would also be integration tests. Of course, not every project lends itself to integration testing and there is certainly a point of diminishing returns as far as test coverage goes. Nevertheless, automated testing is an absolutely mandatory skill that professional software developers of any level should be proficient in. 

## Polyglot Programming

Students should have solid working proficiency in at least two languages, one from a different paradigm. Java and Python. C# and Ruby. C++ and Haskell. The actual languages matter less than having to learn enough of each to build a non trivial project in order to appreciate the strengths, weaknesses, and differences between the languages.

If you're a student and your instructors don't require these, take the initiative to learn these things on your own and incorporate them into your schoolwork on a time available basis. I promise it will pay off in spades come interview time. 

