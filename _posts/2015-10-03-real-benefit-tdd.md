---
layout: post
title: The Real Benefit of Writing Your Tests First
comments: true
published: false
---

The real benefit of test-first or test-driven development is that it allows you to tackle a problem in small, modular chunks. Often 
times, if you're working on a big software task, it can be tough to figure out where to start. You might have multiple ideas on how you could solve the problem, or be unsure about how to architect the different components of the solution. But at least some of the subtasks are probably concrete and clear. Start there. Write some tests for what needs to get accomplished, and then implement the functionality. Test a little, implement a little, back and forth.

Sooner or later, you'll have to make some architectural decisions, but rather than wringing your hands about which 
approach is right, you can make a decision and try it out. If it's wrong, you have a small number of well tested-modules, and any refactoring or modifications you make are protected by your tests.
