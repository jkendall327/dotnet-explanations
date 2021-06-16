<!-- title: Unit testing -->
# Unit testing - [Home](../index.md)

- [Unit testing - Home](#unit-testing---home)
  - [In a nutshell](#in-a-nutshell)
  - [Example](#example)
    - [Naming](#naming)
    - [Expected and actual](#expected-and-actual)
    - [Arrange, act, assert](#arrange-act-assert)

## In a nutshell

Unit tests are code that automatically run other bits of code. They usually check if your code has bugs, or make sure you don't accidentally break old code with new updates.[^1]

## Example

The idea of code that can tell if other code is correct or not might seem magical. Unit tests are actually very mundane. Here is what one looks like:

```csharp
        public int ReturnsFour()
        {
            return 4;
        }

        [Fact]
        public void ReturnsFour_ShouldReturnFour()
        {
            var expected = 4;
            var actual = ReturnsFour();

            Assert.Equal(expected, actual);
        }
```

As you can see, this is incredibly complex and sensitive code - thank god we have tests to prove it works.

The second method here is the unit test. In .NET/C#, test methods usually have an attribute (the line in square brackets above the method name).

But what really makes it a unit test is the last line. All unit tests call some kind of `Assert` method that checks if two values are equal, or if a `bool` is true, or something similar. This is what determines if the unit test passes or fails.

As a result, you can write perfectly useless tests like this:

```csharp
        [Fact]
        public void ReturnsFour_ShouldReturnFour()
        {
            var expected = 4;
            var actual = ReturnsFour();

            Assert.Equal(true, true);
        }
```

This test will always 'pass'.

In the sample above, I've tried to illustrate as many 'best practices' about unit tests as possible. I will now explain them.

### Naming

Naming things well [is famously difficult](https://hilton.org.uk/blog/why-naming-things-is-hard). To counter this, there is a standard naming pattern for unit tests in C#.

1. The name of the method you are testing.
2. What the method 'should' do.
3. (Optional) Under what conditions Step 2 is true.

For example, you might give a test this name: `CountUsers_ShouldReturnZero_WhenDatabaseIsEmpty`.

### Expected and actual

Every unit test is about comparing the result of some operation with what you expect it to be.

It is a good idea to literally use the names 'expected' and 'actual' in tests to make it clear to everyone else who reads your code what your expectations are.

### Arrange, act, assert

I didn't show this in the example because it was too short, but there is also a conventional way to order your unit tests.

First, you *arrange* what you need to perform the test.

Then, you *act* by performing the operation you are testing.

Finally, you *assert* to find out if the result if what you expect.

[^1]: Commonly called a *regression*.
