# Properties

[Home](index.md)

## In a nutshell

Properties are a kind of method that make the `public` and `private` keywords more powerful.

## Explanation

One of the hardest parts of programming is organising your code in a sensible way. While it's very possible to make useful applications in a single long file of code ([here's an example](https://viewsourcecode.org/snaptoken/kilo/), this quickly becomes hard to work with for big projects. 

An important part of object-oriented programming is that a class can choose to hide some of its internal operations from the outside world.

This is what you are doing when you use the `public` or `private` keyword in C#. If a class has a `public` field, any other part of the program can read that data, or edit it. If the field is `private`, only the class itself can see it.

(Advanced note: there are several other visibility modifiers in C#, like `internal` and `protected`. They all just extend the basic idea of public/private.)

Marking a field as public or private is good, but it's also limiting. If another part of the program can read a `public` field, it can also set it to a nonsense value. This is a common source of bugs.

We want, then, a way to separate the ability to read a piece of information from the ability to edit it. A sensible way to do this is with methods.

```
public class MyClass
{
    private string importantInformation;

    public string GetImportantInformation
    {
        return importantInformation;
    }
}
```

Other parts of the program can now see what `importantInformation` is, but they can't edit it. Only `MyClass` can.

We can also use methods to control how other parts of the program edit a field.

```
public class MyClass
{
    private string importantInformation;

    public void SetImportantInformation(string newInformation)
    {
        if (string.IsNullOrEmpty(newInformation))
        {
            return;
        }
        else
        {
            importantInformation = newInformation;
        }
    }
}
```

Other parts of the program can now edit `importantInformation`, but they have to go through this new method first. The method stops nonsense values like a `null` string getting passed in. We can now be a lot more confident that `importantInformation` will always be a sensible value.

You now understand the idea behind properties in C#. Everything else is just convenient syntax for doing these kinds of things in a simpler way.

## Convenient syntax

Consider the following snippet of code.

```
public string ImportantInformation { get; private set; }
```

To other parts of the program, `ImportantInformation` will behave like any other string. They can get its value without any issues. But they won't be able to edit it in any way. In a single line of code, we've made a field that other parts of the program can read but not modify.

The `{ get; private set; }` syntax is very convenient, but there is another form we can use. This snippet of code is identical:

// ...

## Behind the scenes

There is nothing magical at all about the `{ get; private set; }` syntax. When the C# compiler reads this, it simply creates two methods in your class that return and set the value of the field.
