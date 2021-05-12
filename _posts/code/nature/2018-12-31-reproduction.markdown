---
layout: post
title:  "Reproduction"
date:   2018-12-31 13:53:02 -0600
categories: nature genetic algorithms clojure open source free reproduction
description: "How nature recombines multiple individuals to create new individuals"
permalink: /code/nature/reproduction
---

This is where the real value comes in for Genetic Algorithms.
The individuals we selected likely model good solutions to our problem.
In turn, each part of their genetic sequence probably solves part of our problem well.
Within the realm of our example, a bag worth $500 under the weight limit is probably full of items that min-max weight:value, like batteries.
The amount of each item we've taken contributes to the overall quality of the solution, and we want to use that meta-information to build better solutions.

At a conceptual level, _Reproduction_ is the name for the class of functions from 2 or more individuals mapping to 1 or more individuals.
Much like actual biology, we use information from multiple genomes to construct a new genome that is (hopefully) more adept at survival.
To ground it back in a tangible example, by knowing the quantity of portable electronics and batteries purchased in two high-quality solutions, we could hypothesize a new shopping list containing both.

Reproduction takes many forms, and, lacking the physical and chemical constraints of biology, we're free to dream up and invent whatever functions we please.

### Crossover

In the biology we're familiar with, that is human biology, reproduction takes a complete, unmatched group of chromosomes from two parents and fuses them into the complete genetic makeup for a new individual.
We can abstract this concept and turn it into a function that works against our digital genetic sequences.
To make our work simple, we'll take half of the genetic sequence from parent A and merge it with half of the genetic sequence from parent B.
In the interest of minimizing complexity, this is usually done by splitting sequences evenly down the middle.
So, let's look at how this function may work with out shopping example.

| Solution ID  | Hammers | Screwdrivers | Nails  | AA Batteries | Screws |
| :----------- | :------ | :----------- | :----- | :----------- | :----- |
| _Parent A_   | _0_     | _2_          | _50_   | _10_         | _4_    |
| **Parent B** | **7**   | **0**        | **25** | **4**        | **10** |
| Child        | _0_     | _2_          | _50_   | **4**        | **10** |

<br />
As you can see, the child inherits the first three alleles (Hammers, Screwdrivers, and Nails) from parent A.
Parent B contributes two alleles (AA Batteries and Screws).
In cases off odd-length genomes, rounding is just as common as random tie-breakers; however, the primary function remains intact.
Additionally, to save computational effort, generally two complementary individuals will be created.

| Solution ID  | Hammers | Screwdrivers | Nails  | AA Batteries | Screws |
| :----------- | :------ | :----------- | :----- | :----------- | :----- |
| _Parent A_   | _0_     | _2_          | _50_   | _10_         | _4_    |
| **Parent B** | **7**   | **0**        | **25** | **4**        | **10** |
| Child C      | _0_     | _2_          | _50_   | **4**        | **10** |
| Child D      | **7**   | **0**        | **25** | _10_         | _4_    |

### Fitness Based Scanning

Fitness based scanning attempts to make the process of reproduction smarter, while still maintaining a probabilistic nature.
As my [research advisor](https://sun.iwu.edu/~mliffito/) always said, "It's always more efficient to reuse information you already have."
In this case, the fitness score is a prime candidate for reuse.
We already know that solutions with better fitness scores are more likely to be selected from the population, but what if individual alleles were more likely to be chosen too?

Given the following parents:

| Solution ID  | Hammers | Screwdrivers | Nails  | AA Batteries | Screws |
| :----------- | :------ | :----------- | :----- | :----------- | :----- |
| _Parent A_   | _0_     | _2_          | _50_   | _10_         | _4_    |
| **Parent B** | **7**   | **0**        | **25** | **4**        | **10** |

<br />
Assume that parent A has a fitness score of 75, and that parent B has a fitness score of 50.
Now, we can pick alleles with a weighted probability of 75:50, or 60% and 40% respectively.
Unlike crossover, we have no guarantees about contributions by each parent.
It is possible, however unlikely it may be in a given scenario, that we end up with alleles from one parent alone.
Like selection, we're relying on the natural pressures asserted by probability alone.

| Solution ID  | Hammers | Screwdrivers | Nails  | AA Batteries | Screws |
| :----------- | :------ | :----------- | :----- | :----------- | :----- |
| _Parent A_   | _0_     | _2_          | _50_   | _10_         | _4_    |
| **Parent B** | **7**   | **0**        | **25** | **4**        | **10** |
| Child        | _0_     | **0**        | _50_   | _10_         | **10** |

#### Genetic Algorithm Phases

1. [Initialization](/code/nature/initialization)
2. [Fitness Evaluation](/code/nature/fitness-evaluation)
3. [Selection](/code/nature/selection)
4. **Reproduction**
5. [Mutation](/code/nature/mutation)
6. [Generation Advancement and Termination](/code/nature/termination)
