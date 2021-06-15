# Static - [Home](../index.md)

- [Static - Home](#static---home)
  - [In a nutshell](#in-a-nutshell)
  - [A note on terminology](#a-note-on-terminology)
  - [How things work without static](#how-things-work-without-static)
  - [A demonstration of static](#a-demonstration-of-static)
  - [Methods and classes can also be static](#methods-and-classes-can-also-be-static)
  - [Static classes](#static-classes)
  - [Misconceptions about static](#misconceptions-about-static)
  - [When to use static](#when-to-use-static)

## In a nutshell

C# is an object-oriented language. Almost everything you work with is tied to an instance of a type. When you use the static keyword, things are tied to *the type itself*.

## A note on terminology

It is important to understand these two terms:

- Type
- Class

In C#, (almost) every value has a type. For example, the value 'hello' has type `string`, and the value '34' has type `int` or `double`.

The compiler can use a 'type system' to check if we do something that isn't allowed for a type. This is why we can't multiply two `string`s together.

C# has several built-in types, but we can also define our own. Every time we create a class, we create a new type.

We can also define new types in other ways, like creating structs or interfaces. I use classes in this page because they make the best example, but be aware that types and classes aren't the same thing.

## How things work without static

You can use the `new` keyword to create an instance of a class.

```csharp
var sb = new StringBuilder();
```

This creates an instance of `StringBuilder` (a random example of a class).

```csharp
var sb = new StringBuilder();
sb.Length = 23;
```

Here, we are modifying the instance. The `StringBuilder` type is not changed at all: just the `Length` property of the instance.

## A demonstration of static

Consider this class.

```csharp
class MyClass
{
    public static int Counter = 0;
}
```

By declaring this class, we define a type called `MyClass`. Because `Counter` is static, we can access it from anywhere we can access MyClass. We do this with dot-notation:

```csharp
class Program
{
  public void Main()
  {
    Console.WriteLine(MyClass.Counter); // prints "0"

    MyClass.Counter = 6;

    Console.WriteLine(MyClass.Counter); // prints "7"

  }
}
```

Because Counter is part of the type itself, you can't access it through an instance of the type.

```csharp
class Program
{
  public void Main()
  {
    var instance = new MyClass();

    Console.WriteLine(instance.Counter); // this will not compile
    instance.Counter = 6; // this will also not compile

    Console.WriteLine(MyClass.Counter); // this will compile
    MyClass.Counter = 6; // this will also compile
  }
}
```

## Methods and classes can also be static

So far, we've only seen `static` used on a variable (`Counter`).

We can also define static methods:

```csharp
class MyClass
{
    public static int DoMathematics(int x, int y)
    {
      return (x + 16 % 2) * (y - 11 + 2 * x);
    }
}
```

You can only make a method static if it doesn't touch any instance data. Instance data basically means 'anything in a type that isn't static'.

```csharp
class MyClass
{
  public int Counter = 0;

  public static void IncreaseCounter()
  {
    Counter++;
  }
}
```

This example won't compile, because `IncreaseCounter` is static and `Counter` isn't.

As a general rule, static parts of a class can only touch other static code. Static methods usually get data from their arguments.

Non-static code can touch whatever it wants, so it's OK to call a static method from a non-static piece of code.

You access static methods with dot-notation, like static variables.

```csharp
class Program
{
  public void Main()
  {
    MyClass.ComplicatedStaticMethodThatDoesSomething();
  }
}
```

An example of a class with lots of `static` methods is the built-in `Math` class.

## Static classes

You can declare an entire class as `static`.

This just means that everything in the class has to be `static` itself. It doesn't do anything special besides that. This can be useful if you want to stop yourself (or someone else) from forgetting to make a variable or method `static`.

## Misconceptions about static

Static has an unfortunate name which makes a lot of people think it means 'immutable'. This is not true.

When you declare something static, you can still change it.

If you want to make something immutable, declare it as either `readonly` or `const`. I will explain the difference between these two terms in another article.

## When to use static

There is a better solution to most problems than using static.

Static lets you work with methods and data without learning how classes and instances work. This is convenient, but also dangerous. It is very hard to keep track of the state of a static variable when almost everywhere in your program can access it. If you want to use static variables, consider making them `readonly`.

There are other ways you can hurt yourself by using static:

- Static constructors are complicated and can crash your program
- Using static variables in a multi-threaded program can lead to wrong results

Static methods are slightly faster than non-static methods. This difference is minor and you should not worry about it.

You should use static if:

- A method doesn't work on any instance data
