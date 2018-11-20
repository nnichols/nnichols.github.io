---
layout: post
title:  "Testing Addition"
date:   2018-11-20 08:42:26 -0600
categories: testing BDD TDD property test code programming
---

How would you test addition?

No, seriously, how would you go about testing something like addition? 

I know what you're thinking, "It's such a primitive language feature! Why would anyone _actually_ test that?" Obviously, I'm not advocating that you add test cases for every fundamental language feature your codes uses (unless you're writing a programming language, that is). Rather, I want to use this admittedly insane example to talk about testing practices and what gets glossed over and missed.

Before we begin, I want you to give you a reflective assignment as a context primer. Think over your last year of coding, QA testing, product support, etc. Now, ask yourself the three following questions:
1. How many bugs/errors did you discover?
2. How many were caused by trivial or dumb mistakes?
3. How many could have been caught with 1-2 more test cases?

If your experience has been anything like mine, your answers will probably look something like this:
1. How many bugs/errors did you discover? _A handful each sprint_
2. How many were caused by trivial or dumb mistakes? _Most, maybe 80%_
3. How many could have been caught with 1-2 more test cases? _Most, maybe 80%_

Obviously, your mileage may vary. The main observation I've made is twofold: Most bugs are caused by accident, and, once corrected, a test case is usually added to ensure they never come back. Given the amount of concurrent work most systems experience, this is a decent pattern for correction; however, I'd rather talk about prevention.
