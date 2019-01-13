---
layout: post
title:  "Selection"
date:   2018-12-31 13:53:02 -0600
categories: nature genetic algorithms clojure open source free
description: "How nature nominates individuals for reproduction"
permalink: /code/nature/selection
---

If *Fitness Functions* are the probability model that an organism can survive long enough to reproduce, then *Selection* is the use of that model.
By using the fitness scores we assigned in the prior phase, we have a great tool to sample our solution pool.
For example, a solution with a very high value and little weight is more important to hold on to a bag full of hammers.
In most implementations, each individual is selected fairly with a probability of their fitness score against the aggregate fitness score of the entire population.

It's as easy as that.

#### Genetic Algorithm Phases:
1. [Initialization](/code/nature/initialization)
2. [Fitness Evaluation](/code/nature/fitness-evaluation)
3. **Selection**
4. [Reproduction](/code/nature/reproduction)
5. [Mutation](/code/nature/mutation)
6. [Generation Advancement and Termination](/code/nature/termination)
