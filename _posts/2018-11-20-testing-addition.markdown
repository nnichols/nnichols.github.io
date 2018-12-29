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
(defn in-code-test
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

While we're still using enumerated, specified test cases, our testing rigor has increased. Why? Because we're testing _behaviors_ against each other, not just values. In order to get _any_ value from randomized testing, we need to know and understand general behaviors of our programs.

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

In general, if we understand the relationship between our inputs and outputs well enough, we should be able to assert general _properties_ of the function. We evolved from specific inputs rendering a specific output to a general type of input to a general type of output. Now we ask ourselves what must be true for _any_ input? What things do we know about the concept of addition that we can test against our implementation of addition?

```clojure
(deftest another-good-test
  (let [a (pick-a-random-integer)
        b (pick-a-random-integer)
        c (pick-a-random-integer)]
    (testing "Addition is commutative"
      (is (= (add a b) (add b a))))
    (testing "Addition is associative"
      (is (= (add (add a b) c) (add a (add b c)))))
    (testing "0 is the identity element for addition"
      (is (= a (add 0 a))))))         
```

The properties of commutativity and associativity are true, regardless of the values, or even family of values, we use. The above tests are useful since they test the consistency of the function against strong, mathematical invariants. We're no longer tracking attributes of our test data, or making statements that are conditionally true.

### What's Next?

So, we should burn down all of our tests and replace them with __Property Tests__, right?

Not exactly. Drastic action is rarely the answer. In truth, each of the three types of test above are good and necessary when used appropriately. The key is balancing work and payoff, and targeting techniques to fit the problems you're trying to solve. That's generally good advice, but here's how it specifically relates to the problem at hand.

__Enumerative Testing__ is really good for quick sanity checks while coding. It helps you determine if you're on the right track and shows you what is actually happening. They're easy to do and expand upon. On top of that, I fully understand the use of paranoia/'Never Again!' test cases to make _absolutely_ certain the [big, bad bug](https://en.wikipedia.org/wiki/Big_Bad_Beetleborgs "The biggest and baddest bugs") never comes back. Been there, done that.

The real benefit in persistent, enumerative tests are to track your edge cases. In the case of addition, the most common example is overflow. If our function handled floats and doubles, we'd also want some around rounding. If you know when a function breaks down, or where it _should_ break down, it's good to confirm that behavior. In the case of overflow, your implementation could throw an error, hard crash, or return the overflown result. It's useful to know the limitations of your code, and it helps detect underlying changes or assumptions you've made about your language and the metal it runs on.

__Property Testing__ is very strong, but often limited in another way. Most functions have few invariants. Testing them is a powerful and quick way to build surety, but they are few and far between. A lot of practical application needs lack features like commutativity, idempotency, and other $10 Computer Science terms. What holds true for _every_ possible `UPDATE` statement?

I'm not expecting an immediate answer. It's supposed to be a difficult question. These are hard to assert and hard to write, but they cover massive ground. If there are underlying issues in the implementation, the rigidity often causes them to be the first to break. For operations grounded in mathematics, there are usually plenty of theorems you can find on Wikipedia. For more practical operations, you'll be happier the closer your implementation is to the algorithm you're implementing.

__Behavior Testing__ is the nice middle-ground that will capture most of your testing needs, and may or may not require randomized data. Testing a search feature with randomized strings probably won't get you very far, unless you're really interested in the 'Not Found' path of your application. On the flip side, testing a regex against a few hard-coded strings representative of 'likely' scenario families will probably [cause more problems](https://xkcd.com/1171/ "Obligatory xkcd") than it solves.

The relationship between input and output is where most coding starts, and it's a practical place to start your testing. The more your test cases represent behavioral scenarios, the better you'll understand the implementation and use of your functions.

### Takeaways

1. A varied testing strategy will take you farther than dogma
2. Write stronger tests before you just write more tests
3. Use randomized data (and mutation testing, if you desire) to test the assumptions you make

As Dijkstra says, "Testing shows the presence, not the absence of bugs." Therefore, it's important to focus on tests that target likely weak points, make bold claims, and explore as much of the application's problem domain as possible. Humans make mistakes. Your code has bugs, and finding and squashing them is crucial. The point of these test cases isn't to nit-pick through everyone else's code or testing suites, but to help search for bugs wherever they made be found.
