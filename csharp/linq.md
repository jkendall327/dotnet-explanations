# LINQ - [Home](index.md)

- [LINQ - Home](#linq---home)
  - [In a nutshell](#in-a-nutshell)
  - [Terms you will hear used to describe LINQ](#terms-you-will-hear-used-to-describe-linq)
    - [LINQ is about building definitions](#linq-is-about-building-definitions)
  - [Declarative programming](#declarative-programming)

## In a nutshell
LINQ is a set of extension methods that make working on lists of data more convenient.

## Terms you will hear used to describe LINQ
LINQ looks and feels quite different from most other C# code. These differences are often explained through vague terms that are unhelpful for beginners. I will explain them.

### LINQ is about building definitions
People often say that LINQ uses 'deferred execution' or 'lazy evaluation', which are two names for the same idea.

The idea is that with LINQ, you build up a definition of the thing you want to do, which you can then deploy whenever you like. 

```
var myFavouriteNumbers = new List<int>() { 4, 1, 12, 18 };

IEnumerable<int> result = myFavouriteNumbers.Select(x => x * x).Reverse();
```

The second line of this example *does not do anything*. No calculation is performed. No numbers are squared, no list is reversed. `result` is just a definition of various things we want to do to `myFavouriteNumbers` at a later time.

To actually make anything happen, we need to 'trigger' the evaluation. We do this by calling one of a few extension methods on our `IEnumerable`. The most common is `.ToList()`.

```
result = result.ToList();
```

`result` will now contain the values 324, 144, 1 and 16. By transforming the `IEnumerable` into a `List`, we put our definition to work and made it actually evaluate the contents of `myFavouriteNumbers`.

We can re-use the definitions we create with LINQ as much as we want. A result of this is that we can get different results from the same `IEnumerable` if the underlying source of data has changed.

```
var myFavouriteNumbers = new List<int>() { 4, 1, 8, 2 };

IEnumerable<int> result = myFavouriteNumbers.Select(x => x * x).Reverse();

var firstCalculation = result.ToList(); // 4, 64, 1, 16

myFavouriteNumbers.Add(3);
myFavouriteNumbers.Add(9);
myFavouriteNumbers.Add(1);

var secondCalculation = result.ToList(); // 1, 81, 9, 4, 64, 1, 16
```

Each time we called `.ToList()`, it used the values currently in `myFavouriteNumbers` at that exact moment.

## Declarative programming
Declarative programming means that our code only talks about what we want to happen, not how it gets done.

`IEnumerable<int> result = myFavouriteNumbers.Select(x => x * x).Reverse();`

In this line, we simply call `Reverse()` and - by some magic - our list of numbers comes back in the opposite order. We don't have to manually create a new list and iterate over the old one with a for-loop counting backwards.

Declarative programming is an imprecise, stylistic term. You have probably written declarative code before without realising it.