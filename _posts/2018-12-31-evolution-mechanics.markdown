---
layout: post
title:  "Genetic Algorithms Primer"
date:   2018-12-31 13:53:02 -0600
tags: nature genetic algorithms clojure open source free
description: "A high-level overview of the phases that make up Genetic Algorithms"
permalink: /code/nature/evolution-mechanics
---

Genetic Algorithms are simply programs that approximate solutions to mathematically difficult questions by simulating biology.
I know, "simply" is a strong word to use in this case; however, the processes involved are strikingly similar to those you already know.
To explain, let's start with an example not too far away from daily life: shopping.

In my hometown, there was a hardware store that held a unique sale every year.
When you walked in, you were given a brown paper grocery bag and a few simple rules:
1. At checkout time, any item in the bag would be marked down by 11%
2. The items in the bag couldn't pile out over the top
3. The bag had to remain intact
4. The offer was only good for tangible items in the store on the day of the sale

So, being well-educated consumers, we want to get the best discount possible.
How should we formulate a strategy for picking items to go in the bag?
In real life, we tend to use a guiding rule to help make our shopping decisions.
Imagine evaluating how "good" the discount is for various items:
* Cotton stuffing takes up a lot of space, and is very cheap.
* Weed killer is expensive, but might weigh more than the bag can support.
* A ladder can't fit in a grocery bag
* Specialty drill bits are expensive, small, and light.

This type of reasoning, which I like to call "napkin math," guides many of our real-life decisions: navigating, shopping, packing.
With a bit of rigor, we can model this problem with mathematics.
So, how do we quantify "good" and "bad" solutions?
Let's assume we have access to the store's inventory, and can easily model each item in the store.
For example:
```
{item-name: "Precision Screwdriver",
 value: $7.99,
 size: 2.3 cubic inches
 weight: 0.32 ounces
 current-stock: 8}
```

From here, we could compute a relationship weighing each items value against the how much it impacts the bag's ability to fit more items.
For example, we could say the *Precision Screwdriver* has a value of ```$7.99``` and a weighted cost of ```0.736 = 2.3 * 0.32```.
And now all we have to do is find the set of items with the highest value with a weighted cost that doesn't cross a specified threshold.
Easy, right?

Well, unfortunately not.
The above is an example of the [Knapsack Problem](https://en.wikipedia.org/wiki/Knapsack_problem), which belongs to a set of problems in Computer Science known as [NP-Complete](https://en.wikipedia.org/wiki/NP-completeness).
They model many real-world optimization problems, but it is currently unknown if efficient algorithms exist to solve them.
So, what are we to do, and how do Genetic Algorithms fit in?
Let's find out.

## [Initialization](/code/nature/initialization)

A lot of the actual work that goes into initialization happens long before the first line of code processes.
Modeling a problem like "How do I get the best deal from a sale?" and the relevant data, like the value:cost pairs above, informs how initialization actually works.
Instead of multiplying size and weight, we could have added them, or a million different operations.
Likewise, how we choose to incorporate the stock of each item is an important consideration to make beforehand.
Once we finish that, we need to decide how we wish to encode potential solutions to our problem.

For example, how do I, in data, express a bag filled with 9 hammers?
3 screwdrivers?
A ladder, 4 rolls of duct tape, 2 packs of nails, and a candy bar?
Since we're working with Genetic Algorithms, let's try to model them like our own DNA.

In our cells, each "piece" of our genetic code is marked with one of four nucleobases: adenine, cytosine, guanine, or thymine.
You may remember seeing something like this in a Biology class:
```
1: A
2: C
3: C
4: G
5: C
6: T
...
```
Often, you'd be asked to find the matching pairs; however, we can use something similar to build a genome to model solutions to our problem.
```
Hammers: 0
Screwdrivers: 2
Nails: 50
Saw Blades: 2
Sandpaper Sheets: 0
...
```

From here, it's very easy to imagine generating tens, hundreds, or thousands of these candidate solutions randomly.

| Solution ID | Hammers | Screwdrivers | Nails | ... |
| :---------- | :------ | :----------- | :---- | :-- |
| 01224465655 | 0       | 2            | 50    | ... |
| 91982371644 | 7       | 0            | 50    | ... |
| 71222225632 | 1       | 100          | 0     | ... |

<br />
Often, these are known as *Individuals*, and their collection is referred to as a *Population*.
Once we have these solutions, we need to do something with them.

## [Fitness Evaluation](/code/nature/fitness-evaluation)

Obviously, having a potential solution to a problem helps us very little if we don't know how good that solution is.
In the Knapsack Problem, we need to grade our solutions with respect to the value of all items included, how likely they are to break the bag, and if the store has enough items in stock.
So, given each individual's genetic sequence, how can we create a relative measure of quality?
A pseudocode evaluation function might look like the following:
```
score = SUM(items, ((item.count * item.value) / (item.count * item.weight)))
```

In the above example, we would highly value things like portable electronics and lowly value items like insulation.
The relationship you establish between all of your data points is crucial, because it is the criteria we're optimizing against.
For example, the above function does not take into account the limits of our bag.
During initialization, we may have added constraints to prevent bag-breaking solutions or more items than there are available for purchase; however, most evaluation functions allow room for solutions past problem boundaries for two reasons:
1. The creation and modification of individuals may easily allow solutions to fall out of bounds
2. Out-of-bound solutions may contain useful information

That isn't to say they're allowed to cheat and get scored highly.
In those instances, a penalty is often applied.
For example, we could update our above score to account for the bag's weight:
```
score = score / (bag.capacity - items.weight)
```

Obviously, this solution needs to account for dividing by 0; however, it now **greatly** favors solutions close to, but under, the weight limit.
This could be further modified to account for item inventory, or adjusted with weights to each component of a solution differently.
Tuning evaluation functions depends greatly upon the genome and problem on hand.
Mathematic optimization functions involve simple calculations like the above, optimization functions based on real world data with constraints and other considerations is far more difficult.

In parallel in biology would be very hard to compute.
*Fitness Functions* are named as such because they model the probability an organism can survive long enough to reproduce.
Creating a model to determine *that* probability would be very difficult, and is why Actuarial Science has spun off into its own entire field.
Plugging the numbers into an existing model is very easy to do.
As was the case with **Initialization**, a lot of the work is done before coding begins.
Thankfully, using our data model is far easier than creating it.

## [Selection](/code/nature/selection)

If *Fitness Functions* are the probability model that an organism can survive long enough to reproduce, then *Selection* is the use of that model.
By using the fitness scores we assigned in the prior phase, we have a great tool to sample our solution pool.
For example, a solution with a very high value and little weight is more important to hold on to a bag full of hammers.
In most implementations, each individual is selected fairly with a probability of their fitness score against the aggregate fitness score of the entire population.

It's as easy as that.

## [Reproduction](/code/nature/reproduction)

This is where the real value comes in for Genetic Algorithms.
The individuals we selected likely model good solutions to our problem.
In turn, each part of their genetic sequence probably solves part of our problem well.
Within the realm of our example, a bag worth $500 under the weight limit is probably full of items that min-max weight:value, like batteries.
The amount of each item we've taken contributes to the overall quality of the solution, and we want to use that meta-information to build better solutions.

At a conceptual level, *Reproduction* is the name for the class of functions from 2 or more individuals mapping to 1 or more individuals.
Much like actual biology, we use information from multiple genomes to construct a new genome that is (hopefully) more adept at survival.
To ground it back in a tangible example, by knowing the quantity of portable electronics and batteries purchased in two high-quality solutions, we could hypothesize a new shopping list containing both.

Reproduction takes many forms, and, lacking the physical and chemical constraints of biology, we're free to dream up and invent whatever functions we please.

### Crossover

In the biology we're familiar with, that is human biology, reproduction takes a complete, unmatched group of chromosomes from two parents and fuses them into the complete genetic makeup for a new individual.
We can abstract this concept and turn it into a function that works against our digital genetic sequences.
To make our work simple, we'll take half of the genetic sequence from parent A and merge it with half of the genetic sequence from parent B.
In the interest of minimizing complexity, this is usually done by splitting sequences evenly down the middle.
So, let's look at how this function may work without shopping example.

| Solution ID  | Hammers | Screwdrivers | Nails  | AA Batteries | Screws |
| :----------- | :------ | :----------- | :----- | :----------- | :----- |
| *Parent A*   | *0*     | *2*          | *50*   | *10*         | *4*    |
| **Parent B** | **7**   | **0**        | **25** | **4**        | **10** |
| Child        | *0*     | *2*          | *50*   | **4**        | **10** |

<br />
As you can see, the child inherits the first three alleles (Hammers, Screwdrivers, and Nails) from parent A.
Parent B contributes two alleles (AA Batteries and Screws).
In cases off odd-length genomes, rounding is just as common as random tie-breakers; however, the primary function remains intact.
Additionally, to save computational effort, generally two complementary individuals will be created.

| Solution ID  | Hammers | Screwdrivers | Nails  | AA Batteries | Screws |
| :----------- | :------ | :----------- | :----- | :----------- | :----- |
| *Parent A*   | *0*     | *2*          | *50*   | *10*         | *4*    |
| **Parent B** | **7**   | **0**        | **25** | **4**        | **10** |
| Child C      | *0*     | *2*          | *50*   | **4**        | **10** |
| Child D      | **7**   | **0**        | **25** | *10*         | *4*    |

### Fitness Based Scanning

Fitness-based scanning attempts to make the process of reproduction smarter, while still maintaining a probabilistic nature.
As my [research advisor](https://sun.iwu.edu/~mliffito/) always said, "It's always more efficient to reuse information you already have."
In this case, the fitness score is a prime candidate for reuse.
We already know that solutions with better fitness scores are more likely to be selected from the population, but what if individual alleles were more likely to be chosen too?

Given the following parents:

| Solution ID  | Hammers | Screwdrivers | Nails  | AA Batteries | Screws |
| :----------- | :------ | :----------- | :----- | :----------- | :----- |
| *Parent A*   | *0*     | *2*          | *50*   | *10*         | *4*    |
| **Parent B** | **7**   | **0**        | **25** | **4**        | **10** |

<br />
Assume that parent A has a fitness score of 75, and that parent B has a fitness score of 50.
Now, we can pick alleles with a weighted probability of 75:50, or 60% and 40% respectively.
Unlike crossover, we have no guarantees about contributions by each parent.
It is possible, however unlikely it may be in a given scenario, that we end up with alleles from one parent alone.
Like selection, we're relying on the natural pressures asserted by probability alone.

| Solution ID  | Hammers | Screwdrivers | Nails  | AA Batteries | Screws |
| :----------- | :------ | :----------- | :----- | :----------- | :----- |
| *Parent A*   | *0*     | *2*          | *50*   | *10*         | *4*    |
| **Parent B** | **7**   | **0**        | **25** | **4**        | **10** |
| Child        | *0*     | **0**        | *50*   | *10*         | **10** |

## [Mutation](/code/nature/mutation)

In biology, errors happen all the time.
When our genetic sequences are copied are transferred, things can go wrong and a different allele can be encoded.
If we write our programs well and make strong functional contracts, we don't have to worry about these types of problems; unless we choose to.

In Generic Algorithms, mutation is nigh-ubiquitous.
At a high level, mutation is any function from a single individual to another individual.
Since many genetic sequences are binary, this is accomplished through a flipping operator.
Given the legal values of an allele, if that allele is selected for mutation, its value is randomly swapped with another random legal value.
That being said, any allele-centric function may be used.
In the case of an integer genome, like the one in our example, we could exchange it with an incrementing or decrementing function.
In either case, this is what the result could look like:

| Solution ID  | Hammers | Screwdrivers | Nails  | AA Batteries | Screws |
| :----------- | :------ | :----------- | :----- | :----------- | :----- |
| *Original*   | *0*     | *2*          | *50*   | *10*         | *4*    |
| **Mutant**   | **1**   | **2**        | **50** | **10**       | **4**  |

<br />
Note that the genetic sequences are nearly identical.
This is intentional, as many Genetic Algorithms choose a mutation rate equal to 1 divided by the length of the genetic sequence; however, [better strategies may exist.](http://neuro.bstu.by/ai/To-dom/My_research/Papers-0/For-research/Needle/back.pdf)
Mutation, as a class of operators, serves the purpose of breaking out of local optima.
As successful genetic patterns are copied, they may begin to prematurely converge towards a good-but-not-great region.
With small, infrequent changes, solutions may hop towards nearby potential regions that could have been ignored.

## [Generation Advancement and Termination](/code/nature/termination)

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
Reproduction operators, like crossover and fitness-based scanning, will return identical solutions, adding to the problem.
Mutation operators may change very small portions of solutions, but the probability of those differences propagating is small.
Why?
Well, unless the fitness score of a modified solution is extreme,  the selection process will still be heavily weighted towards the convergent solution.

It should be noted that convergence is not necessarily a bad thing.
If the optimal, or a near-optimal, solution has been found, we want our genetic sequences to begin mirroring traits of that solution.
However, we want to balance this desire with a need for a wide and diverse gene pool.
If our solutions are converging towards good-but-not-great solutions, we need genetic diversity to help draw our population towards other promising regions of our search space.
