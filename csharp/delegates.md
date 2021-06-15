<!-- title: Delegates -->
# Delegates - [Home](../index.md)

- [Delegates - Home](#delegates---home)
  - [In a nutshell](#in-a-nutshell)
  - [Explanation](#explanation)
  - [Method signatures](#method-signatures)
  - [Using delegates](#using-delegates)
  - [Actually using a delegate](#actually-using-a-delegate)
  - [Treating methods like variables](#treating-methods-like-variables)
  - [Other syntax that you might see](#other-syntax-that-you-might-see)
  - [Multicast delegates](#multicast-delegates)
  - [Why care about delegates?](#why-care-about-delegates)
  - [Further learning](#further-learning)

## In a nutshell

A delegate is like a `List<>` for methods.

## Explanation

Think about a `List<int>`:

```csharp
var myList = new List<int>() {4, 6, 1, 10, 11};
```

Using a `List<>` lets you group several values together into one variable. This lets you do things to all of them at once — with a `List<int>`, you can `Sum()` the list with a single command.

An important thing about a `List<int>` is that you can only put `ints` in it. If you put in a `double` or a `string`, the C# compiler will tell you that you've made a mistake right away. This is type-safety, and stops you from making silly mistakes.

A delegate is like a `List<>` for methods.

With a delegate, you can group methods into one variable. This lets you do things with all of them at once.

A delegate is type-safe, like a `List<>`. When you make a delegate, you have to say what kind of methods it accepts. If you then put in another kind of method, the C# compiler will tell you that you've made a mistake right away.

But how do you decide what 'kind' of methods this list should contain? To understand that, we need to understand method signatures.

## Method signatures

Consider the following method.

```csharp
public int AddThenSquare(int x, int y)
{
    var sum = x + y;
    return sum * sum;
}
```

When you're learning C#, you focus on what's between the curly braces. This is the *body* of the method.

But for delegates, we care about the signature, which is the bit at the top.

`public int AddThenSquare(int x, int y)`

Only two parts of the signature matter for delegates:

- The arguments.
- The return type.

For two methods to go in the same delegate, they need to have the same arguments and return type.
The name of the method and its visibility (whether it's `public` or `private`) aren't important.

The *arguments* are the variables the method asks for. More specifically, we only care about the types of the arguments. It doesn't matter that `AddThenSquare` names one argument 'x' and the other 'y'. All that matters is that they are both `int`s.

The return type is what the method gives back. In this case, it's also an `int`. But it could be `void`, or anything else.

(Advanced note: the CLR can differentiate methods based on just their return type, but C# chooses not to support this.)

If we want to add `AddThenSquare` to a delegate, we need a delegate that says 'I will take in any method that accepts two `int`s and gives back one `int`'.

## Using delegates

We have to define the methods our delegate accepts before we can use it.

```csharp
    delegate int ExampleDelegate(int x, int y);
```

This looks very similar to defining a method. There are two differences:

- We use the `delegate` keyword at the start.
- Instead of adding a method body, we put a `;` after the arguments.

We can now add methods to our delegate.

```csharp
class MyExampleClass
{
    public int AddThenSquare(int x, int y)
    {
        var sum = x + y;
        return sum * sum;
    }

    delegate int ExampleDelegate(int x, int y);

    public void MyMethod()
    {
        var myDelegate = new ExampleDelegate(AddThenSquare);
    }
}
```

This class has a method, a delegate, and another method which uses them both.

`delegate int ExampleDelegate(int x, int y);` defines a *kind* of delegate.

`var myDelegate = new ExampleDelegate(AddThenSquare);` creates an *instance* of the delegate we defined.

(Advanced note: we could actually define our delegate outside of `MyExampleClass`. This is because behind the scenes, all delegates are actually classes.)

`myDelegate` now contains `AddThenSquare`. Because delegates are like `List<>`s, we can add multiple methods to it.

```csharp
class MyExampleClass
{
    public int AddThenSquare(int x, int y)
    {
        var sum = x + y;
        return sum * sum;
    }

    public int SubtractThenSquare(int x, int y)
    {
        var sum = x + y;
        return sum * sum;
    }

    delegate int ExampleDelegate(int x, int y);

    public void MyMethod()
    {
        var myDelegate = new ExampleDelegate(AddThenSquare);
        myDelegate += SubtractThenSquare;
    }
}
```

`myDelegate` now contains both methods. You can remove a method from a delegate by using `-=`.

(Advanced note: the `+=` syntax is shorthand for the [Delegate.Combine](https://docs.microsoft.com/en-us/dotnet/api/system.delegate.combine?view=net-5.0) method.)

## Actually using a delegate

Nothing happens when you just add methods to a delegate, in the same way nothing happens when you add numbers to a `List<int>`.

To make our delegate do something, we use the delegate's `Invoke` method.
We defined our delegate as taking two `int`s, so we have to give it two `int`s to invoke it. It will also give us one `int` back, since that's how we defined it.

```myDelegate.Invoke(4, 5);```

This will run through every method in `myDelegate`, giving them each the arguments 4 and 5. We're not doing anything with the return values (81 and 1, in this example), but we could if we wanted.

## Treating methods like variables

People care about delegates because they let us treat methods like variables. More specifically, we can pass a method into another method.

```csharp
    delegate void ExampleDelegate(int x);

    public void DoTheMethodTwice(ExampleDelegate method, int argument)
    {
        method.Invoke(argument);
        method.Invoke(argument);
    }
```

This method does something with a delegate, without any knowledge of what methods the delegate actually contains.

(Advanced note: in fact, the delegate might be `null` and not contain any methods at all. If so, calling `Invoke` will crash the program.)

Delegates let us write *methods about methods*.

## Other syntax that you might see

Writing `.Invoke()` all the time is annoying, so C# has a shorthand.

```csharp
    delegate void ExampleDelegate(int x);

    public void DoTheMethodTwice(ExampleDelegate method, int argument)
    {
        method(argument);
        method(argument);
    }
```

I don't use this syntax. It looks identical to calling a normal method, and delegates should be treated differently than methods.
You are welcome to use this syntax, just be careful with it.

You will also see something like this.

`method?.Invoke(argument);`

The question mark is the [null-conditional operator](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#null-conditional-operators--and-). Basically, it stops your program crashing if you use an empty delegate. You have to use `.Invoke` if you use the null-conditional operator.

## Multicast delegates

I've used the metaphor of a `List` throughout this page. But there's a difference between a [`System.Delegate`](https://docs.microsoft.com/en-us/dotnet/api/system.delegate?view=net-5.0) and a [`System.Delegate.MulticastDelegate`](https://docs.microsoft.com/en-us/dotnet/api/system.multicastdelegate?view=net-5.0).

`System.Delegate` can only hold one method at a time. Only the `MulticastDelegate` can contain multiple methods.
The reason it's called 'multicast' is because when you add a delegate to one, it technically creates a new copy
The reason for this split is historical. When you use the `delegate` keyword, it will always be the `MulticastDelegate`.

## Why care about delegates?

When you first learn about them, it's hard to imagine where delegates would be useful.

You won't create many delegates yourself. Microsoft has created convenient shortcuts for the most common use-cases, like lambda expressions, events, `Action<>` and `Func<>`. Learning about those topics will make delegates easier to understand in retrospect.

More broadly, delegates are the basis of functional programming in C#. This page isn't the place to explain functional programming, but just know that it's an important topic.

## Further learning

The best explanation of delegates is [Jamie King's YouTube playlist](https://www.youtube.com/playlist?list=PLAE7FECFFFCBE1A54). You should watch this entire playlist.
