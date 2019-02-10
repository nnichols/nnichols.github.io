---
layout: post
title:  "nature"
date:   2018-12-31 13:53:02 -0600
categories: nature genetic algorithms clojure open source free
description: "A free and easy-to-use genetic algorithms library for clojure"
permalink: /code/nature/intro
---

As promised on the [README](https://github.com/nnichols/nature), nature aims to be "A simple genetic algorithms library for Clojure."

Now, why is that the promise I've chosen to make?

1.  Genetic Algorithms are a pet interest of mine, and [I've researched them earlier.](https://digitalcommons.iwu.edu/jwprc/2013/oralpres8/1/)
2.  Genetic Algorithms are inherently flexible, and can make use of any number of genomes, fitness functions, and reproduction functions.
    A small, focused library that handles the core mechanics of evolution, selection, and performance monitoring lets the user focus on the elements they care about.
3.  While often modeled and built in OO languages, Genetic Algorithms are well suited for functional paradigms.
    I specifically chose Clojure because I like it and work in it on a day-to-day basis.

The first promise is one of personal preference.
There's not much useful discussion one can have on the subject of "Why do you like the things you like?"

The second promise falls into the category of pragmatic philosophy, which borders on personal preference.
In my experience, a library that tries to offer everything for everyone fails for two main reasons.

1.  People, especially software people, are complex and have complex preferences and needs.
    It's impossible to anticipate all of them, harder to handle most of them, and harder yet to solve some of them well.
    A small, composite library offers the tools people can use to solve their problems.
    In short, I wanted to offload the complexity of solving your interesting problem/business feature on to you.
    In exchange, I'll handle the complexity of updating and maintaining some of the tools you use to do that job.
2.  People have bad memories and wildly different ideas on how to name things.
    Extensive libraries often run into the phenomena of feature cloning where they're used.
    It's not because the problem isn't being solved well, or it conflicts with some other piece of the puzzle, but because none of the programmers know or remember it exists.

The third promise is a hypothesis, which requires evidence to defend.
At the core, Genetic Algorithms can be thought of as multiple, composite functions:

-   Individual -> Number (Fitness Evaluation)
-   Population -> One or More Individuals (Selection)
-   One or More Individuals -> One or More Individuals (Reproduction)
-   Individual -> Individual (Mutation)
-   Population -> Population (Generation Advancement)

With well-defined expectations of the domain and range, these functions become much easier to decompose and examine as individual elements.
Changing how your Genetic Algorithm goes about its business is simple a process of supplying different functions that adhere to the same promises.
The reproduction implementation can easily exist without having to know, care, or behave around things like typing of alleles.
In short, we care about _how_ individuals are generated, selected, modified, and scored.
Aside from the results of our operations, we rarely tend to care about the individuals of a population and don't have much room to leverage OO principals to trim the fat.

Now, I do believe there is one glaring defect in what I just said.
Technically speaking, the phases of Genetic Algorithms _aren't_ functions.
Why not?
Because they rely on some form of entropy, they're non-deterministic, and therefore they're not functions.
Sure, you could make an argument about seeding, pseudorandom number generation, or a deterministic universe, but those are weak arguments in the realm of conceptual mathematics.

In either case, nature exists, and we can learn from it.
If Genetic Algorithms are new to you, a primer on [evolution mechanics](/code/nature/evolution-mechanics) is helpful pre-reading.
Below, you'll find the core phases of a Genetic Algorithm side-by-side with our implementation.

1.  [Initialization](/code/nature/initialization)
2.  [Fitness Evaluation](/code/nature/fitness-evaluation)
3.  [Selection](/code/nature/selection)
4.  [Reproduction](/code/nature/reproduction)
5.  [Mutation](/code/nature/mutation)
6.  [Generation Advancement and Termination](/code/nature/termination)
