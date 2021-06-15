# Properties / getter-setters - [Home](index.md)

- [Properties / getter-setters - Home](#properties--getter-setters---home)
  - [In a nutshell](#in-a-nutshell)
  - [Explanation](#explanation)
  - [Convenient syntax](#convenient-syntax)
    - [Explaining the syntax](#explaining-the-syntax)
    - [A common bug](#a-common-bug)
    - [An even more convenient syntax](#an-even-more-convenient-syntax)

## In a nutshell
Properties are a kind of method that make the `public` and `private` keywords more powerful.

## Explanation
In object-oriented programming, a class can hide its internal operations from the outside world. This is called 'encapsulation'.

This is what you are doing when you use the `public` or `private` keyword in C#. If a class has a `public` field, any other part of the program can read that data, or edit it. If the field is `private`, only the class itself can see it.

(Advanced note: there are several other visibility modifiers in C#, like `internal` and `protected`. They all just extend the basic idea of public/private.)

`Public` and `private` are good, but limiting. If another part of the program can read a `public` field, it can also set it to a nonsense value. This is a common source of bugs.

We need a way to separate *reading* a piece of information from *editing* it. We can do this with methods.

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

We can also use methods to control how a field gets changed.

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

Other parts of the program can now edit `importantInformation`. Our new method, however, stops nonsense values like a `null` string getting passed in.

You now understand the idea behind properties in C#. Everything else is just convenient syntax for making these kinds of methods.

## Convenient syntax
If you open up Visual Studio, type the word `propfull` and then hit the tab key twice, VS will write this chunk of code for you:

```
private string importantInformation;

public string ImportantInformation
{
    get { return importantInformation; }
    set { importantInformation = value; }
}
```

This should look similar to the examples above. We have a private field, `importantInformation`, and a public *property* beneath which controls access to it.

### Explaining the syntax
When the compiler sees the above example, it simply replaces it with two normal methods, called `get` and `set`. That's all properties are -- methods.

The `value` keyword is unique to properties. It's whatever your field is going to be set to. You can do validation checks on it, for example,

`if (string.IsNullOrEmpty(value)) { ... }`

### A common bug
Note that `importantInformation` and `ImportantInformation` aren't the same. It's convention to use lowercase for the field, and pascalcase for the property.

If you mix up the names of the field and the property, you can cause serious bugs. The most common is to use the name of the property in the property itself.

```
private string importantInformation;

public string ImportantInformation
{
    get { return ImportantInformation; }
    set { ImportantInformation = value; }
}
```

This will crash your program whenever anything tries to access `ImportantInformation`. Because the `set` method refers to the property itself, the program will get stuck in an endless loop. You will quickly run out of memory and crash without even a stacktrace.

### An even more convenient syntax
You might have noticed that in the examples above, we don't actually do anything interesting to keep the field safe. We don't have any validation checks.

If this is the case, there's actually an even simpler syntax for writing a property.

```
public string ImportantInformation { get; set;}
```

This is equivalent to the above examples.