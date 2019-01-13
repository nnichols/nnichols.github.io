---
layout: post
title:  "Initialization"
date:   2018-12-31 13:53:02 -0600
categories: nature genetic algorithms clojure open source free
description: "How nature spins up new individuals"
permalink: /code/nature/initialization
---

Problem and sequence modeling is the brunt of the work when spinning up a new Genetic Algorithm.
Most packages, like nature, come pre-equipped with tools for reproduction, mutation, metrics, etc.
Choosing the representation for the problem you want to solve requires some degree of domain knowledge, something that a library can never replace.
Thankfully, there are many common patterns and trends to make this process simpler.

To begin, we need to know what the typical individual looks like.
In nature, a [spec](https://github.com/nnichols/nature/blob/master/src/nature/spec.clj) has been provided.

```clojure
(s/def ::genetic-sequence
  (s/and coll?
         not-empty?))

(s/def ::guid string?)

(s/def ::parents
  (s/and coll?
         not-empty?))

(s/def ::age integer?)

(s/def ::fitness-score number?)

(s/def ::individual
  (s/keys :req-un [::genetic-sequence
                   ::guid
                   ::parents
                   ::age
                   ::fitness-score]))
```

So, what do we use each of the keys to represent, and why do we care?

* **genetic-sequence** - A collection of data that represents a solution candidate to the problem modeled by your fitness function. This is the foundational piece of an individual.
* **guid** - A string representing a [v1 UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). In may implementations, individuals are anonymous, as they are rarely inspected when used to solve problem instances; however, for academic purposes, a guid can be useful to trace lineage while researching the operations of various reproduction operators.
* **parents** - A collection of one or more strings representing the genetic sequences used to create the individual. With **guid**, this allows for tracing lineage throughout a genetic algorithm's execution. If the individual is created during the initialization phase, it is assigned to a vector containing the string ["Initializer"](https://github.com/nnichols/nature/blob/master/src/nature/population_presets.clj).
* **age** - The number of generations an individual has been a member of. For practical purposes, this is also uncommonly used, but serves the purposes of convergence research. A high average age may help indicate early convergence or a highly localized optima. A low average age may indicate solutions that are steadily increasing in quality relative to prior generations.
* **fitness-score** - A number representing how well the **genetic-sequence** solves the problem at hand. It takes whatever value the fitness function returns when applied to the **genetic-sequence**.

Most of these will be automatically supplied for you by nature in the function `build-individual`; however, your genetic sequence encoding and your fitness function are up to you as the expert of your problem domain.
In the interests of discussing initialization, we'll stick to genetic sequences for now and examine the `generate-sequence` function.

```clojure
(defn generate-sequence
  "Creates a genetic sequence of `sequence-length` elements,
  where each item is in the collection of `alleles`"
  [alleles sequence-length]
  (repeatedly sequence-length #(rand-nth alleles)))
```

The `alleles` collection should consist of the base values any position your **genetic-sequence** may contain.
Each individual position within the **genetic-sequence** will be filled with an equally-weighted sampling from `alleles` until `sequence-length` positions have been filled.
In common applications, this usually means either binary or an integer range.
Since they're common, named examples exist in the [population-presets](https://github.com/nnichols/nature/blob/master/src/nature/population_presets.clj) namespace.
Once created, `build-individual` will evaluate the **genetic-sequence** against your **fitness-function**.

#### Genetic Algorithm Phases:
1. **Initialization**
2. [Fitness Evaluation](/code/nature/fitness-evaluation)
3. [Selection](/code/nature/selection)
4. [Reproduction](/code/nature/reproduction)
5. [Mutation](/code/nature/mutation)
6. [Generation Advancement and Termination](/code/nature/termination)
