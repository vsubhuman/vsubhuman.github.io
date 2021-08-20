---
layout: post
title:  "On sets and predicates"
date:   2021-08-21 01:15:00 +0300
tags: functional paradigm types
ord: '00'
---

Back in my junior days, I remember still figuring out how to conceptualise many of the coding-related stuff in my head. Especially when getting to learn new approaches, different languages and paradigms, etc. There was a day when I have been dealing around with some sets and some predicates for the task I have been solving at the moment. And there was a moment of some realization about the commonalities of these types.

The realisation and the idea itself are not that complex or profound, hardly anyone would find it exciting, I think. But back in the day it had changed the way I look at data-structures and has helped me to conceptualise their nature and behavior in my head in the simplest atomic terms. I thought it might be a good topic for the very first post on the personal blog, and might help someone else out there who is trying to achieve some compartmentalization right now.

{% include toc %}

## Quick catch-up

Just to make sure the context is clear let's clarify what we are talking about here.

A `Set` is that [abstract data-structure type](https://en.wikipedia.org/wiki/Set_(abstract_data_type)) that people are using to filter-out duplicates 99% of the time. That `new HashSet<>()` in Java or `{1, 2, 3}` in Python or `new Set(['a', 'b', 'c'])` in JS or `#{:a :b :c :d}` in Clojure. We can add some elements to a set (by mutating or producing a new instance, doesn't matter) and if the element hasn't been there before - the set will have a bigger size after that presumably; and then we can easily check if some element **IS** in that set or not; or remove some element from that set and then the set must say that the element ISN'T there.

A `Predicate` is that [mathematical logic thing](https://en.wikipedia.org/wiki/Predicate_(mathematical_logic)) which is any function that returns a `boolean` value. A function that might be consuming anything and returns only `true` or `false`. Usually any function that starts with `is*` is meant to be a predicate, if it's not - something is probably wrong. Examples of course are `Array.isArray` in JS, `Double.isNaN` in Java, `.isnumeric()` in Python, or `even?` in Clojure. Since Java 8 there's of course a whole separate type [Predicate](https://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html) used a lot with the introduction of lambdas so you can do now stuff like you'd do it in JS, for example `.filter(x => x != null)`, which is one of the main usages for predicates, I assume, in languages that has functional bits.

## So what's so special about them?

<emph>
    The special part is that the sets and predicates are <b>a whoooole lot like each other</b>, really,
    <br/>when you start thinking about them.
</emph>

This is the part where anyone who has been studying the sets in math or the type theory are looking like this:

![The heck?](/assets/images/000-the-heck.jpg)

Because for them, it's like saying 2+2 is 4. But if it's not like that for you, then some of the below might be interesting.

This might be helpful to someone who might still have this more of a **mechanical** idea of a set, who starts thinking about the trees or hashes and buckets, or iterations, when they think about a set. That surely was the problem for me. And this thinking into the ways how predicates and sets are alike and how they are different gave me a better understanding of the nature of these types and when they should be used mechanically and when it should rather be a more abstract logical approach. Might be useful for some as well.  

## A programming set is a logical predicate

In programming the sets are always two-sided: they have a collection data-structure side, and they have a functional predicate side. You can use a set to dump a lot of elements into it and then iterate over it with no duplicates or use it to test if some specific elements are included.

The testing part is what interests us today. Creating a set with two elements like `s = {1, 5}` and then testing `x in s` is virtually indistinguishable from `lambda x: x == 1 or x == 5` from the caller's perspective. A set with 100 unique elements is just a very specific case of a predicate that tests the argument against all the elements and uses some implementation hacks to do that test quickly.

## Any predicate is a logical set

We have all seen (and potentially admired) how math sometimes defines sets ([math sets](https://en.wikipedia.org/wiki/Set_(mathematics))), something like:
> "Set of all natural numbers N"

And that's it! That does the trick. Although, we know that those are really just some predefined commonly used sets, like global constants in maths. But reality is not that far off anyways, because there's this whole ["Set builder notation"](https://en.wikipedia.org/wiki/Set-builder_notation) thing going and it even has the ["Sets defined by a predicate"](https://en.wikipedia.org/wiki/Set-builder_notation#Sets_defined_by_a_predicate) section.

```
{ x | x > 0 }
```

That's not some weird `OR` there, that's a **lambda**! WAT?! That is exact same thing as:

```
x => x > 0
```

Which is of course a predicate as **we** know it.

Any predicate function divides the entire material universe into two parts: those objects that evaluate to `true` and those that evaluate to `false`. This automatically defines two sets of objects: `P` and `not(P)`! We can also call the false set `!P` as programmers might be more used to, or `¬P` from logic. The predicate might be as general or as specific as you want it to be. Define it as "Matter belonging to the current universe" - now you have the set `P` of **everything ever** and the set `!P` of nothing.

Those also happen to be the constant predicates `* => true` and `* => false` that always produce the same result for any argument (at least within this universe) and can be useful in some cases where a predicate is required. 

![Everything burrito](/assets/images/001-burrito.jpg)

Define the predicate as "Strings of text that has been used so far as an identifier to register users in this specific software system". Now you have a valid global set `P` of all your user IDs and the accidental set `!P` of everything else in the universe which is not a user ID in your app.

## Ok, but what can we do with it?

<emph>
    We can start rethinking, what it really means to <b>BE</b> a set or to <b>USE</b> a set in the programming sense.
</emph>

### Constructing

Imagine we want to create a set of all numbers from -1000 to 1000, of course we can do something like:

```javascript
const set = new Set();
for (let i = -1000; i <= 1000; i++) {
  set.add(i);
}
```

Or something like:

```python
set(range(-1000, 1001))
```

And those operations will be executed immediately and take up the memory.

Or we could do something like this:

```javascript
const set = (i) => i >= -1000 && i <= 1000;
```

As long as you want to use a set as a predicate to test inclusion - this lambda is functionally equal to both previous examples.

Experienced readers can also right away notice that what we are talking about is pretty much exactly how the `range()` function works in Python, the one we are using in an example above. When you create a `Range` instance - it does not evaluate numbers right away, instead it works as a **lazy** collection that will produce the actual values only when required, like when passing it into `set()` or `list()`. But it can be used as a predicate right away:

```python
r = range(-10, 11)
-20 in r # False
0 in r   # True
20 in r  # False
```

**This is a mainstream example of exactly that predicate laziness we are talking about!** It's a very special case to work with number ranges and it works perfectly for that specific case, but the important part is to be able to see the idea nad the pattern behind that specific case. When we are talking about predicates - they are not limited to numbers, of course:

```javascript
const setOfAllUserLogins = (s) =>
  typeof s === 'string' && db.checkUserExistsByLogin(s);
```

How 'bout that? Now you have a "set" of all user logins in your system and not only it's lazy in the sense of not querying the entire load of values from the database in advance, but it's also dynamic - if a new user is registered in parallel, this set will reflect that change without reconstruction. You might not want this in some cases of course, but in some other cases that might be exactly what you are looking for.

In case you don't want the set to be dynamic, one way to solve that lazily is to use memoization:

```javascript
const lazySetOfAllUserLogins = memoize(
  (s) => typeof s === 'string' && db.checkUserExistsByLogin(s),
);

// will always return the same result now for same argument
const loginExists = await lazySetOfAllUserLogins(someLogin);
```

It is still "dynamic" in the sense that underlying values might change until you have first observed them, but once you have received some answer - that answer will always be the same for same argument within same "set" instance. In case you really want to have a static set that perfectly represents the underlying data **at the moment of instantiation** and not at the moment of the call - the laziness is not applicable, of course.

<foot>
  A good recommended read is: <a href="https://bluepnume.medium.com/async-javascript-is-much-more-fun-when-you-spend-less-time-thinking-about-control-flow-8580ce9f73fc">"Async javascript is much more fun when you memoize"</a>
</foot>

### Updating

Yeah, but it's not that interesting to be constructing a predicate to use it as a predicate and be calling it a set after that, right? In fact the "Set as an abstract data type" article describes the <a href="https://en.wikipedia.org/wiki/Set_(abstract_data_type)#Operations">set <troll title="lol, set" /> of operations usual for various types of sets</a>. Let's check some of them out.

#### Union, Intersection, Difference

These are the easiest for us to replicate with predicates cuz they straight-up come from set-theory and like no other prove again how the notion of "predicates" is close to the notion of "sets". 

A union is of course a combination of two sets into one, like: 

```clojure
(union #{1 2} #{2 3}) ;; #{1 2 3}
```

Using clojure here for the cleanest demonstration of the concept. We can see that union is saying an element is within the result set if it is **within EITHER input sets**. A union of two predicates is exactly the same:

```javascript
const pUnion = (p1, p2) => x => p1(x) || p2(x);

const p1 = x => x >= 1 && x <= 5;
const p2 = x => x >= 4 && x <= 10;
const p = pUnion(p1, p2);

p(1) && p(9) && p(4) && p(5) && p(6); // true
```

The `pUnion` function takes two predicates and creates a new predicate that will resolve to true any element that either one of the input predicates would also resolve to true.

An intersection of two sets is saying an element is within the result set if it is **within BOTH input sets**:

```clojure
(intersection #{1 2 3} #{2 3 4}) ;; #{2 3}
```

```javascript
const pIntersection = (p1, p2) => x => p1(x) && p2(x);
```

A difference of two sets is saying an element is within the result set if it is **within the first set and is NOT within the second one**:

```clojure
(difference #{1 2 3} #{3 4 5}) ;; #{1 2}
```

```javascript
const pDifference = (p1, p2) => x => p1(x) && !p2(x);
```

#### Add, Remove

Ok, those set-theory operations are really basic, but it's probably not that easy to just randomly add or remove something from a predicate, right? WRONG!

```javascript
new Set([1,2,3]).add(4); // Set(4) {1, 2, 3, 4}
```

```javascript
const pAdd = (p, x) => pUnion(p, a => a === x);

const p = pAdd(x => x >= 1 && x <= 3, 4);
p(1) && p(2) && p(3) && p(4); // true
```

WITCH! I'm guessing you already know what "remove" will look like:

```javascript
(s = new Set([1,2,3])).delete(3); s; // Set(2) {1, 2}
```

```javascript
const pRemove = (p, x) => pDifference(p, a => a === x);

const p = pRemove(x => x >= 1 && x <= 3, 3);
p(1) && p(2); // true
p(3); // false
```

And it very obviously holds the expectation that any removed item can be added again:

```javascript
const p = pAdd(pRemove(x => x >= 1 && x <= 3, 3), 3);
p(1) && p(2) && p(3); // true
```

## Iterating and domains

Ok, but now we are entering the real discussion of how could we ever check if a predicate is a "subset" of another predicate. And this concept is very tightly coupled with the concept of "Iteration" as well. Obviously we can't just iterate all elements of one predicate to check them in another. This is where the notion of **domains** enters the scene.

Imagine we have an integer number predicate, something like `x => x % 2 === 0` so all even numbers. Is there a way for us to get hold of all integer numbers that match this predicate and are included in the "set"? Of course there is! All we need to do is iterate every single possible integer number, for example between `Number.MIN_SAFE_INTEGER` and `Number.MAX_SAFE_INTEGER` in JS, and test them against the predicate. Technically doable and solves the problem. Is it practical, tho? Probably not, depends on the size of the expected set. But then what if we have a string predicate like `s => s.length % 2 === 0` so all strings with even length. How many strings there are that would match this predicate? Around infinite. It would be technically non-computable to try and iterate all possible options to match them against the predicate.

This is where we have to introduce **domains** so we can continue defining other set functions for predicates, and it is explainable, why we need it. Mathematical sets have two possible natures: they can be [defined by enumeration](https://en.wikipedia.org/wiki/Set-builder_notation#Sets_defined_by_enumeration), directly specifying the elements included in that set, or [defined by a rule](https://en.wikipedia.org/wiki/Set-builder_notation#Sets_defined_by_a_predicate), specifying the predicate. The sets defined by enumeration naturally have their domain built right into their definition, as they only contain the enumerated elements. But the sets defined by a rule have their domain defined explicitly right in the same builder, either as a global thing like `x ∈ N` for all natural numbers or something more specific.

The example of a set definition we used above: `{ x | x > 0 }`, as we mentioned works almost exactly like a lambda function in programming as says something like <emphl>"For any `x` - is `x > 0`"</emphl>. This predicate set definition also [allows to specify the domain of the argument](https://en.wikipedia.org/wiki/Set-builder_notation#Specifying_the_domain) in two interesting ways:  

1. Either `{ x ∈ E | x > 0 }`
2. Or `{ x | x ∈ E ^ x > 0 }`

Which exactly mirrors how the type of a function argument would be checked in a strongly-typed language versus a weakly-typed language. Something like:

1. `(x: ValidNumber) => x > 0`
2. `x => isValidNumber(x) && x > 0`

<emph>
  Now we can see that programming sets historically have been representing only the mathematical sets defined by the enumeration, while the mathematical sets defined by a predicate are mostly represented in programming through function definitions (and predicate functions specifically) and the usual mathematical set operations are represented as boolean logic that can be applied to predicates.
</emph>

It might be useful to understand this idea that **enumerable things are easily mapped into programming concepts**, while something unbound and not naturally easily enumerable can be represented as an inherently computationally-lazy thing, like a function, which is not presumed to "contain" anything really by itself, but can give you an answer in response to some argument, which will tell you whether this element is within this "virtual set" or not.

Representing mathematical predicate sets as predicate functions allows at least some way to map this non-computable math concept into programming, but it detaches it from all the set-related functionality that is so familiar to programmers from sets as data types. This set-like functionality then gets separately re-learned in type theory (as a computable alternative to the set theory) as any type is basically a predicate that separates things of one kind from anything else and inherited types are subsets of their parent types. It might have been just me, but it really seemed like all the teaching material for programming has been lacking at least some mentioning of how all these different concepts really boil-down to all the same good ol' sets that are not that different logically from the "Set" data-structures that we are so used to.

### Enumerable vs Lazy

We have now discussed how there are two fundamental types of mathematical sets: enumerable (computable) and non-enumerable (presumably non-computable) and those are handled through enumerable vs lazy programming concepts. This is the key to understanding all the ways how a predicate domain can be handled in programming.

#### Enumeration

Enumeration presumes an existence of a closed bounded domain with a computable size that can be iterated entirely if so needed (e.g. all built-in bounded integer numbers). For example, you might have predicates that operate on strings that are non-enumerable as a whole type, but most probably you don't care about every single string in existence, most probably you have a specific collection of values already present in the storage of your system, like usernames, or some tags, or category names, or post titles, or something like that, that you are operating on. Or maybe you have a number predicate, but you don't care about every single valid integer number, you only care about a known range of numbers that you are working with, which would be much more practical to enumerate over.

This basically boils-down to the idea that if you already might have a enumerable computable set of values that bounds your entire domain - then within that domain you can work with non-enumerable predicate sets and achieve enumeration. So in order to demonstrate this way of defining further set operations on predicates we will assume an existence of an enumerable set `DOMAIN` that can be iterated and covers the requirements of a specific problem.

#### Lazy

Lazy approach presumes an open unbound domain with a non-computable size that cannot be practically iterated in its entirety (e.g. all possible strings). In this case, if it is impossible to achieve an enumerable domain, the operations on predicate sets can just result in another new predicate function which is then used on-demand whenever a new element needs to be checked.

### Subset

```clojure
(subset? #{2 3} #{1 2 3 4}) ;; true
```

If it is possible to get an enumerable set `DOMAIN` that bounds your domain, then a predicate can be checked to be a subset of another predicate like this:

```javascript
// returns true if p1 is subset of p2
function isSubset(DOMAIN, p1, p2) {
  // every element of the domain that is within p1 must also be within p2
  return DOMAIN.filter(p1).every(p2);
}

// "all elements above 3" is subset of "all elements above 2"
isSubset([1,2,3,4,5], x => x > 3, x => x > 2) // true
```

If enumerable domain is impossible, a lazy option could be handled like this:
```javascript
// test if p1 is a subset predicate of p2
const pIsSubset = (p1, p2) => x => !p1(x) || p2(x);

const pTestCorrect = pIsSubset(x => x > 5, x => x > 1);
const pTestIncorrect = pIsSubset(x => x > 0, x => x > 10);

pTestIncorrect(5); // false
```

This one might not be as obvious right away, because the idea here is that you are not getting the answer about the subsets immediately, but rather you are producing a test-predicate that guarantees you: **if you can ever find an argument that will evaluate to `false` - then the initial proposition is NOT true.**  In this case the value `5` is a **proof** that the provided predicates don't match the subset check. So we can say that a predicate might be a subset of another predicate **for all tested values**, which might be all we care about, because if our system only went to check these available values - they denote the lazy subdomain for the used sets and for this specific case.

### Filter, Map

Filter is not really a separate operation even, filtering any set by a predicate is just an **intersection** of an enumerable set with a non-enumerable set. So filter of a predicate by another predicate is nothing more than an intersection of those two, which we already discussed above, e.g.:

```javascript
function filter(DOMAIN, p1, p2) {
  return DOMAIN.filter(p1).filter(p2);
}
```

```javascript
const pFilter = pIntersection;

// Filter all values above zero to only have values below ten
const p = pFilter(x => x > 0, x => x < 10);

p(1) && p(9); // true
p(0) || p(10); // false
```

**Map** is more interesting tho, because map potentially transforms one set into a completely different set of potentially whole different type. It can be very easy handled with an enumerable domain, of course:

```javascript
function map(DOMAIN, p, f) {
  return DOMAIN.filter(p).map(f)
}
```

Which is really basic, of course, because we have established that any predicate-set P in enumerable domain `DOMAIN` is equal to just filtering the domain set with that predicate. If it's impossible to have an enumerable domain, though, that's where it gets interesting.

Mapping a set to a different type implies you now have a predicate function that accepts arguments of that new type, e.g.:

```
Set<A>.map(f: A => B): Set<B>
Set<B>.has(b: B)
```

An enumerable set `Set<A>` is by itself a proof of its entire eager domain - i.e. all elements within the set can be just listed and prove that they belong to the set. Mapping a set like that with a function `f: A => B` means turning it into an eager domain proof of type `Set<f(A)>` aka `Set<B>`.

A lazy predicate set `P<A>` is not a proof of its entire domain, it's only the functionality to lazily check various elements and see if they are a part of the domain or not. The proof is not contained within the set itself but needs to be supplied from outside to test the domain. And the immediate problem is that in the regular mapping type `S<A>.map(A => B): S<B>.has(B)` the type of the proof changes from `A` to `B` and function `A => B` does not provide any path from `B` back to `A` to be able to utilise the testing mechanism of `S<A>` again.

So this type of mapping is impossible for lazy predicates. But a different type of mapping is possible instead:

```
P<A>.map(f: B => A): P<B>
P<B>.test(b: B)
```

Reverses mapper function allows us to map the resulting domain onto the original domain and be able to operate lazily on it.

```typescript
const pMap = <A, B>(p: (A) => boolean, f: (B) => A) => x => p(f(x));

const p = pMap((x: number) => x > 2 && x < 5, (s: string) => s.length);
p('abc') && p('abcd'); // true
p('ab') || p('abcde'); // false
```

The predicate-set that includes only numbers 3 and 4 gets mapped into a predicate-set that includes any strings that are 3 or 4 characters long. That is a different but in its own way extremely powerful kind of mapping, because it would be computationally impossible to map (or flat-map) an enumerable set `#{3, 4}` into a set of all possible strings of that length with a function `number => string`, but it is possible to map a lazy set `x == 3 || x == 4` into a lazy set with a function `string => number`.

It might be not that obvious again on how it could be useful at all, but it's a good concept to have in mind to always remember, next time when you think you might need to query a whole set of some IDs or names from the database and then map it into something else and keep in memory to perform some checks - you are doing enumerable-set mapping; and instead if it's possible to create an **inverse** of that mapping function - you can do lazy-set mapping and construct a predicate that will lazily on-demand map your entities back ot the relevant namd or ID and then check the DB just for this one value.

### Other domain operations

#### Is empty

Empty check can be constructed in a similar way to the subset operation:

```typescript
const pNot = p => x => !p(x);
const pIsEmpty = pNot;

const pTestCorrect = pIsEmpty(() => false);
const pTestIncorrect = pIsEmpty(x => x > 0);

pTestIncorrect(10); // false
```

Any predicate `P` can be considered empty if there's not a single element that would evaluate to `true` which means any `P` is empty if `!P` is always true. So the proof-checker for `isEmpty(P)` is same as just `not(P)` - if you can find any element for which `not(P)(e)` would return `false` - then the original predicate-set `P` is **NOT** empty.

#### Clear

Clearing any predicate-set is as easy as trying to find an intersection with an empty set:

```typescript
const pClear = p => pIntersection(p, () => false);
```

Logically correct, but also might be simplified into just replacing any predicate with the empty set:

```typescript
const pEmpty = _ => false;
const pClear = _ => pEmpty;

const p = pClear(x => x > 0);
p(10); // false
```

#### Pick, Pop

Operations like pick (peek) and pop of course can be implemented only on top of some enumerable domain, because they require outputting proof with no proof input. The `pick` is too simple to be interesting:

```typescript
const pPick = (DOMAIN, p) => DOMAIN.find(p);
```

But the `pop` is interesting in that it requires a side effect mutation of the probed domain. Two options to implement it:

```javascript
const pPop = (DOMAIN, p) => {
  const el = DOMAIN.find(p);
  return [el, pRemove(p, el)];
}

let p = x => x > 10;

// Popping same exact domain multiple times changes the response
[x, p] = pPop([5,10,15,20,25,30], p); // [15, f]
[x, p] = pPop([5,10,15,20,25,30], p); // [20, f]
[x, p] = pPop([5,10,15,20,25,30], p); // [25, f]
[x, p] = pPop([5,10,15,20,25,30], p); // [30, f]
[x, p] = pPop([5,10,15,20,25,30], p); // [undefined, f]
```

```javascript
const pset = {
  p: x => x > 10,
  pop: function (DOMAIN) {
    const el = DOMAIN.find(this.p);
    this.p = pRemove(this.p, el);
    return el;
  }
}

pset.pop([5,10,15,20]); // 15
pset.pop([5,10,15,20]); // 20
pset.pop([5,10,15,20]); // undefined
```

An example of a predicate pop operation that does not actually change the domain it operates on, but instead changes its own condition.

#### Fold (Reduce)

Remainder that a `fold` (or `reduce`) operation **usually** takes some collection of elements `e`, initial element `x0`, and the function of type `(x, e) => x`, where types `x` and `e` might be similar or different. E.g.:
```javascript
[2,3,4].reduce((a, b) => a * b, 10); // 10 * 2 * 3 * 4 = 240
[2,3,4].reduce((s, b) => s + b, ''); // '' + 2 + 3 + 4 = "234"
````

Fold over an enumerable domain is nothing interesting and turns into a regular fold over a filtered set (as discussed above), but an idea of a lazy fold is way more interesting and might look something like that:

```javascript
const pFold = (p, f, x0) => {
  const res = x => [x, factory(x)];
  const factory = v => x => p(x) ? res(f(v, x)) : null;
  return factory(x0);
};

let f = pFold(x => x > 0, (a,b) => a * b, 3)
  
// Lazy function folds the argument on its last state
// and returns the next step function with new state
[x, f] = f(4); // [12, f]
[x, f] = f(2); // [24, f]
[x, f] = f(3); // [72, f]
[x, f] = f(1); // [72, f]

// Elements that are not in the predicate-set cannot produce valid fold
f(-1); // null
f(0); // null
```

The predicate of the set in this case only filters any lazy-incoming elements so that items not from the set are not producing valid fold steps. Or alternatively:

```javascript
const pset = {
  p: x => x > 0,
  lazyFolder: function (f, x0) {
    const p = this.p;
    return {
      v: x0,
      lazyFold: function (x) {
        return p(x) ? (this.v = f(this.v, x)) : null;
      }
    }
  }
};

const psetFolder = pset.lazyFolder((a, b) => a * b, 3);
psetFolder.lazyFold(4); // 12
psetFolder.lazyFold(2); // 24
psetFolder.lazyFold(3); // 72
psetFolder.lazyFold(1); // 72
```

## Conclusion

In this weird post we have discussed these points:

1. There are two main kinds of sets in math: enumerable and non-enumerable, defined by a predicate rule.
2. Enumerable sets from math have been historically represented in programming as the usual `Set` data-structure with a bunch of related operations.
3. Non-enumerable sets from math are represented in programming as the ability to create predicate functions.
4. Set operations for non-enumerable sets are at best represented in programming as higher order functions and the boolean logic on predicates. And only the basic set-theory operations like union, intersection, and difference are getting mentioned for predicates often enough, specifically in the sense of sets.
5. It might be useful to understand the nature of this commonality between the `Set` data-structures and the predicate functions as both related to representing different kinds of math sets in programming.
6. Thinking, how some usual set operations can be implemented lazily and/or on predicates leads to some interesting patterns and ideas. Which then allows to see that, for example, filtering a regular set with a predicate is nothing else than making an intersection of two sets, adding an element to a set is similar to making a union of two sets, proving a set is empty is similar to proving the inverse of that set holds every single possible elements, and the mapping function can be inverted to work on predicates.

Hopefully it might help someone to change the way they think about some programming patterns in their work.

Cheers! 👋