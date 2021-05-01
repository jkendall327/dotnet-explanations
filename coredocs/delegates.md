# Delegates

## In a nutshell

A delegate is like a `List<>` for methods.

## Why care about delegates?

You probably won't create many delegates yourself when writing C#. There are only a few common use cases for them, so Microsoft created convenient shortcuts for us: lambda expressions, events, `Action<>` and `Func<>`. It's still important to understand delegates, however. It makes understanding those shortcuts a lot easier. Delegates are the backbone of functional programming in C#.

## Explanation

Think about a `List<int>`. You have probably used one like this before:

```
var myList = new List<int>() {4, 6, 1, 10, 11};
```

Using Lists lets you group several values together into one variable. This makes it easy to do things to all of them at once -- with a `List<int>`, for example, you can `Sum()` up the entire list.

But the really useful thing about a `List<int>` is that you can only put `ints` in it. If you try to put a `double` or a `string` or anything else in one, it won't work. The C# compiler will tell you that you've made a mistake right away. This is called type-safety and stops you from making a lot of silly mistakes.

A delegate is like a `List<>` for methods.

With a delegate, you can group as many methods as you want into one variable. This lets you do things with all of them at once -- usually, this is 'invoking' them (which just means to call them).

A delegate is type-safe in exactly the same way that a `List<int>` is. When you make a delegate, you have to say right away what kind of methods you want to put in it. If you then try to put another kind of method in there, the C# compiler will tell you that you've made a mistake right away.

But how do you decide what 'kind' of methods this list should contain? What does that even mean? To understand that, we need to understand method signatures.

## Method signatures

Consider the following method.

```
public int AddThenSquare(int x, int y)
{
    var sum = x + y;
    return sum * sum;
}
```

When you're learning C#, you probably spend most of your time focusing on the stuff between the {}, which actually does stuff. This is called the *body* of the method.

The signature, on the other hand, is the bit at the top.

`public int AddThenSquare(int x, int y)`

The name of the method and its visibility (whether it's `public` or `private`) aren't important.

Only two parts of this matter for delegates:
* The arguments.
* The return type.

The *arguments* are the variables the method takes in. More specifically, we only care about the types of the arguments. It doesn't matter that `AddThenSquare` calls one argument 'x' and the other 'y'. All that matters is that it takes in two `int`s.

The return type is what the method gives back. In this case, it's also an `int`. But it could just as easily be `void` or anything else.

For two methods to go in the same delegate, they need to have the same arguments and return type.

(Advanced note: the CLR can differentiate methods based on just their return type, but C# chooses not to support this.)

If we want to add `AddThenSquare` to a delegate, then, we need a delegate that says 'I will take in any method that accepts two `int`s and gives back one `int`'. 

## Using delegates

We have to define the kind of methods a delegate can accept before we actually use it. This is the syntax for that.

```
    delegate int ExampleDelegate(int x, int y);
```

This looks very similar to defining a method. There are two differences:
* We use the `delegate` keyword at the start.
* Instead of adding a method body with {}, we just add a `;` after the arguments.

Once we have done this, we can use our delegate like a `List<>` and start adding methods to it. Here is an example.

```
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

(Advanced note: we can actually define a delegate outside of `MyExampleClass`. This is because behind the scenes, all delegates are actually classes themselves.)

`myDelegate` now contains `AddThenSquare`. Because delegates are like lists, we can actually add as many methods as we want to it. Let's think up another method with the same signature.

```
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

`myDelegate` now contains both methods. How wonderful! And useless. To make our delegate actually do something, we need to *invoke* it. This just means calling it.
Because our delegate asks for two `int`s, we have to give it two `int`s to invoke it. It will also give us one `int` back, as its signature says.

```myDelegate.Invoke(4, 5);```

This will run through every method in `myDelegate`, giving them each the arguments 4 and 5. Each method will return a value (81 and 1, in this example). But we're not doing anything with those variables, so they just get lost forever. How sad! And useless.

## Multicast delegates

I've used the metaphor of a `List` throughout this page because I think it makes delegates easier to understand.

It's important, however, to note the difference between a `System.Delegate` and a `System.Delegate.MulticastDelegate`.

`System.Delegate` isn't, actually, like a `List<>` at all. It can only hold one method at a time. This still lets you treat a method like a variable, but you can't build up a list of lots of methods.

Only the `MulticastDelegate` can build up a list of methods.

I didn't explain this at the start because, in practical terms, it doesn't really matter. When you use the `delegate` keyword, it will always be the `MulticastDelegate`. The only reason that there is a split between `Delegate` and `MulticastDelegate` is historical. I imagine that if Microsoft could rewrite .NET from scratch, they would combine these two classes.

It's still important (and I think interesting) to know about this difference. You might get asked about it by an interviewer who wants to feel clever.

## Further learning

You now understand the basic idea of delegates. They are a way to put methods of the same signature in a variable. This lets us pass methods around, manipulate them, or invoke them all at once.

The best explanatory source for delegates I have ever found is [Jamie King's YouTube playlist](https://www.youtube.com/playlist?list=PLAE7FECFFFCBE1A54) on them. I cannot reccomend watching this entire playlist strongly enough.