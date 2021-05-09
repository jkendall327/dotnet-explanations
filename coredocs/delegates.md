# Delegates

## In a nutshell

A delegate is like a `List<>` for methods.

## Explanation

Think about a `List<int>`:

```
var myList = new List<int>() {4, 6, 1, 10, 11};
```

Using Lists lets you group several values together into one variable. This lets you do things to all of them at once -- with a `List<int>`, you can `Sum()` the list with a single command.

An important thing about a `List<int>` is that you can only put `ints` in it. If you put in a `double` or a `string`, the C# compiler will tell you that you've made a mistake right away. This is type-safety, and stops you from making a lot of silly mistakes.

A delegate is like a `List<>` for methods.

With a delegate, you can group methods into one variable. This lets you do things with all of them at once.

A delegate is type-safe, like a `List<>` is. When you make a delegate, you have to say what kind of methods it accepts. If you then put in another kind of method, the C# compiler will tell you that you've made a mistake right away.

But how do you decide what 'kind' of methods this list should contain? To understand that, we need to understand method signatures.

## Method signatures

Consider the following method.

```
public int AddThenSquare(int x, int y)
{
    var sum = x + y;
    return sum * sum;
}
```

When you're learning C#, you focus on what's between the {}. This is the *body* of the method.

The signature is the bit at the top.

`public int AddThenSquare(int x, int y)`

The name of the method and its visibility (whether it's `public` or `private`) aren't important.

Only two parts of the signature for delegates:
* The arguments.
* The return type.

For two methods to go in the same delegate, they need to have the same arguments and return type.

The *arguments* are the variables the method asks for. More specifically, we only care about the types of the arguments. It doesn't matter that `AddThenSquare` calls one argument 'x' and the other 'y'. All that matters is that it takes two `int`s.

The return type is what the method gives back. In this case, it's also an `int`. But it could be `void`, or anything else.

(Advanced note: the CLR can differentiate methods based on just their return type, but C# chooses not to support this.)

If we want to add `AddThenSquare` to a delegate, we need a delegate that says 'I will take in any method that accepts two `int`s and gives back one `int`'. 

## Using delegates

We have to define the methods a delegate accepts before we actually use it.

```
    delegate int ExampleDelegate(int x, int y);
```

This looks very similar to defining a method. There are two differences:
* We use the `delegate` keyword at the start.
* Instead of adding a method body, we put a `;` after the arguments.

Once we have done this, we can start using our delegate.

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

This class has a method, `AddThenSquare`, a delegate, and another method which uses them both.

The line `delegate int ExampleDelegate(int x, int y);` defines a *kind* of delegate.
The line `var myDelegate = new ExampleDelegate(AddThenSquare);` creates an *instance* of the delegate we defined.

(Advanced note: we could actually define our delegate outside of `MyExampleClass`. This is because behind the scenes, all delegates are actually classes.)

`myDelegate` now contains `AddThenSquare`. Because delegates are like `List<>`s, we can add multiple methods to it.

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

`myDelegate` now contains both methods. You can remove a method from a delegate by using `-=`.

## Actually using a delegate

Nothing happens when you just add methods to a delegate.

To make our delegate actually do something, we need to *invoke* it. We do this with the delegate's `Invoke` method.
Because our delegate asks for two `int`s, we have to give it two `int`s to invoke it. It will also give us one `int` back, as its signature says.

```myDelegate.Invoke(4, 5);```

This will run through every method in `myDelegate`, giving them each the arguments 4 and 5. We're not doing anything with the return values (81 and 1, in this example).

## Treating methods like variables

People care about delegates because they let us treat methods like variables. More specifically, we can pass a method into another method.

```
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

```
    delegate void ExampleDelegate(int x);

    public void DoTheMethodTwice(ExampleDelegate method, int argument)
    {
        method(argument);
        method(argument);
    }

```

I don't use this syntax. It looks identical to calling a normal method, and delegates should be treated differently than methods.
You are welcome to use this syntax, just be careful with it.

You will also see something like this often.
`method?.Invoke(argument);`
The question mark is the null-forgiving operator, a topic of its own. Basically, it avoids your program crashing if you try to use a delegate while it's empty.
You have to use `.Invoke` if you use the null-forgiving operator.

## Multicast delegates
I've used the metaphor of a `List` throughout this page. But there's a difference between a `System.Delegate` and a `System.Delegate.MulticastDelegate`.

`System.Delegate` can only hold one method at a time. Only the `MulticastDelegate` can build up a list of methods.
The reason for this split is historical. When you use the `delegate` keyword, it will always be the `MulticastDelegate`.

## Why care about delegates?

One reason delegates are hard to understand is because it's hard to imagine where they would be useful.

In truth, you probably won't create many delegates when writing C#. Microsoft has created convenient shortcuts for the most common use-cases, like lambda expressions, events, `Action<>` and `Func<>`. Learning about those topics will probably make delegates easier to understand in retrospect. 

More broadly, delegates are the basis of functional programming in C#. This page isn't the place to explain functional programming, but just know that it's an important topic.

## Further learning

The best explanation of delegates is [Jamie King's YouTube playlist](https://www.youtube.com/playlist?list=PLAE7FECFFFCBE1A54). You should watch this entire playlist.