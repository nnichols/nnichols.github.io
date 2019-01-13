---
layout: post
title:  "Fitness Evaluation"
date:   2018-12-31 13:53:02 -0600
categories: nature genetic algorithms clojure open source free
description: "How nature scores individuals and assigns fitness scores"
permalink: /code/nature/fitness-evaluation
---

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


#### Genetic Algorithm Phases:
1. [Initialization](/code/nature/initialization)
2. **Fitness Evaluation**
3. [Selection](/code/nature/selection)
4. [Reproduction](/code/nature/reproduction)
5. [Mutation](/code/nature/mutation)
6. [Generation Advancement and Termination](/code/nature/termination)
