# Static - [Home](index.md)

- [Static - Home](#static---home)
  - [In a nutshell](#in-a-nutshell)
  - [How things work without static](#how-things-work-without-static)
  - [A demonstration of static](#a-demonstration-of-static)

## In a nutshell

C# is an object-oriented language. Almost everything you work with is tied to an instance of a class. When you use the static keyword, things are tied to *the class itself* instead.

## How things work without static

When you use the `new` keyword, you create an instance of a class.

```
var sb = new StringBuilder();
```

This creates an instance of the `StringBuilder` class.

```
var sb = new StringBuilder();
sb.Length = 23;
```

Here, we are modifying the instance. The actual `StringBuilder` class is not changed at all: just the instance.

## A demonstration of static

Consider this simple class.

```
class MyClass
{
    public static int Counter = 0;
}
```