---
layout: post
title:  "Termination"
date:   2018-12-31 13:53:02 -0600
categories: nature genetic algorithms clojure open source free
description: "How nature terminates and returns information"
permalink: /code/nature/termination
---

Once a sufficiently large pool of individuals has been created by reproducing other solutions and mutating the results, we repeat ourselves.
The new solutions must be scored, selected, reproduced, and then mutated.
This cycle is ultimate architecture pattern of Genetic Algorithms.
The passing of each generator can include transition functions of its own, this time on the scale of entire populations.

To preserve the best solution(s), and ensure the algorithm's quality does not decrease over time, the elite individual from each generation is typically copied into the next generation.
This may be further extended, copying the top *n* individuals between generations, but this must be balanced between preserving quality and premature convergence.

In either case, this process often repeats until one of three criteria is met:
1. A pre-determined number of generations has passed
2. A fitness threshold is crossed by the elite or by the average individual
3. Convergence

The first criteria is straightforward.
Without intervention, this cycle could execute indefinitely, and execution time can easily be controlled artificially.
The number of generations varies from application to application, but often reaches into the hundreds.
Likewise, the second criteria is pretty straightforward.
We're only interested in solutions that beat a benchmark we know or have hypothesized.
Keep in mind that this criteria may lead to infinite execution if the criteria is unachievable or if the solutions converge beforehand.

So, what is convergence?
It is the state when every individual in a population inherits the same genetic sequence and every solution is identical.
This is a very hard state to break out of.
Reproduction operators, like crossover and fitness based scanning, will return identical solutions, adding to the problem.
Mutation operators may change very small portions of solutions, but the probability of those differences propagating is small.
Why?
Well, unless the fitness score of a modified solution is extreme,  the selection process will still be heavily weighted towards the convergent solution.

It should be noted that convergence is not necessarily a bad thing.
If the optimal, or a near-optimal, solution has been found, we want our genetic sequences to begin mirroring traits of that solution.
However, we want to balance this desire with a need for a wide and diverse gene pool.
If our solutions are converging towards good-but-not-great solutions, we need genetic diversity to help draw our population towards other promising regions of our search space.

#### Genetic Algorithm Phases:
1. [Initialization](https://nnichols.github.io/code/nature/initialization)
2. [Fitness Evaluation](https://nnichols.github.io/code/nature/fitness-evaluation)
3. [Selection](https://nnichols.github.io/code/nature/selection)
4. [Reproduction](https://nnichols.github.io/code/nature/reproduction)
5. [Mutation](https://nnichols.github.io/code/nature/mutation)
6. **Generation Advancement and Termination**
