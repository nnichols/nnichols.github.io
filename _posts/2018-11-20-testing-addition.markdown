---
layout: post
title:  "Testing Addition"
date:   2018-11-20 08:42:26 -0600
categories: testing BDD TDD property test code programming
---

How would you test addition?

No, seriously, how would you go about testing something like addition? 

I know what you're thinking, "It's such a primitive language feature! Why would anyone _actually_ test that?" Obviously, I'm not advocating that you add test cases for every fundamental language feature your codes uses (unless you're writing a programming language, that is). Rather, I want to use this admittedly insane example to talk about testing practices and what gets glossed over and missed.

Before we begin, take this reflective assignment as a context primer. Think over your last year of coding, QA testing, product support, etc. Now, ask yourself the three following questions:
1. How many bugs/errors did I discover?
2. How many were caused by trivial or dumb mistakes?
3. How many could have been caught with 1-2 more test cases?

If your experience has been anything like mine, your answers will probably look something like this:
1. How many bugs/errors did I discover? _A handful each sprint_
2. How many were caused by trivial or dumb mistakes? _Most, maybe 80%_
3. How many could have been caught with 1-2 more test cases? _Most, maybe 80%_

Obviously, your mileage may vary. The main observation is twofold: Most bugs are caused by accidents and dumb mistakes, and, once corrected, a test case is usually added to ensure they never come back. Given the amount of concurrent work most systems experience, this is a decent pattern for correction; however, I'd rather talk about prevention.

So, let's assume we've been given the requirements to write the addition operator. It's an easy enough task, and we finish before it's time to think about where we should get lunch today. Now, all we have to do is test it and ship it. So, where do we begin? 

### Enumerative Testing

If we're being honest with ourselves, our first test cases probably looked like this:

```clojure
(defn dirty-test
  [a b]
  (println (add a b)))
```

These quick tests __should__ never make it into production, but we've all made that mistake once or twice. These are just quick sanity checks, but they are effectively no different than the following:

```clojure
(deftest dirty-test
  (testing "I can add 2 numbers"
    (is (= 4 (add 2 2)))))
```

This question is the core of each testing philosophy: If I run my test cases, what can I confidently assert is true? With the above, I can safely say 2 + 2 = 4, and __that is it.__ If I want to increase my confidence in my addition function, I have to enumerate more test cases. 

```clojure
(deftest dirty-test
  (testing "I can add 2 numbers"
    (is (= 4 (add 2 2)))
    (is (= 4 (add 1 3)))
    (is (= 4 (add 3 1)))
    (is (= 4 (add 4 0)))))
```

I think we all see where this is going. In order to get a high degree of confidence, we have to write and execute an _insane_ amount of test cases. So, in practice, it never happens. Writing and executing the tests, that is. __Enumerative testing__ is _far, far more common_ than anyone wants to admit to, because we all know it's the quick and dirty solution that we swear we're only going to do this one time because the deadlines are tight and our users are breathing down our necks. They __really, really__ want to add things, dude. 

Additionally, each test case is very weak. They naively cover single problem instances, and there's no direction or reason behind each one aside from, "Well, it's something the user could want to do." The two most glaring issues with that sentiment are:
1) They rely on the programmer to anticipate user behaviors
2) They rely on the programmer to anticipate edge cases

So, what can we do to improve our testing?

### Behavior Testing

I want to pause and mention that this _is not_ the meant to be the same as Behavior Driven Development. I'm talking about _program behaviors_. The more we know about our program, the more we know how it should behave. The above tests hinted at the answer. We can generalize those cases into a few things we know about the relationship between the inputs and outputs of addition.

```clojure
(deftest better-test
  (testing "Adding two positive numbers makes a bigger positive number"
    (is (< 1 3 (add 1 3))))
  (testing "Adding a larger positive number and a smaller negative number, makes a positive number"
    (is (< -1 0 (add -1 3) 3)))
  (testing "Adding a smaller positive number and a larger negative number, makes a negative number"
    (is (< -3 (add 1 -3) 0 1)))
  (testing "Adding two positive numbers makes a bigger negative number"
    (is (< (add -1 -3) -1 -3))))    
```

So, while we're still using enumerated, specified test cases, our testing rigor has increased. Why? Because we're testing _behaviors_ against eachother, not just values. In order to get _any_ value from randomized testing, we need to know and understand general behaviors of our programs.

```clojure
(deftest even-better-test
  (let [a (pick-a-random-integer)
        b (pick-a-random-integer)]
    (cond 
      (and (pos? a) (pos? b))
        (testing "Adding two positive numbers makes a bigger positive number"
          (is (< a (add a b)))
          (is (< b (add a b)))
      (and (neg? a) (neg? b))    
        (testing "Adding two negative numbers makes a bigger negative number"
          (is (< (add a b) a))
          (is (< (add a b) b)))
      ...  
```

Our new tests presuppose very little. We assert these behaviors are true for __any__ integer, not just the ones we've listed. We know that our implementation _behaves_ the same way that addition should, and every observation and execution of these tests builds our confidence more and more. So, aside from running our test suite ad infinitum, how else can we build our confidence?

### Property Testing

In general, if we understand the behaviors of

he properties of commutativiy and associativity are true of addition, regardless of the values we use. 

What things do we know about the concept of addition that we can test against our implementation of addition? 
