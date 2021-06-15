<!-- title: Interfaces -->
# Interfaces - [Home](../index.md)

- [Interfaces - Home](#interfaces---home)
  - [In a nutshell](#in-a-nutshell)
  - [The problem](#the-problem)
  - [What is an interface?](#what-is-an-interface)
  - [Implementing an interface](#implementing-an-interface)
  - [Fixing the original problem](#fixing-the-original-problem)
  - [Dependency injection](#dependency-injection)
  - [When to use interfaces](#when-to-use-interfaces)
  - [Where to go from here](#where-to-go-from-here)

## In a nutshell

Interfaces let you say what you want your code to do without specifying how it does it.

## The problem

Imagine a program that lets people register accounts and edit their username.

```csharp
public void ChangeUsername(int userID, string newName)
{
    var user = new UserDetailsDatabaseLoader.Load(id);
    user.Name = newName;
}
```

We use `DatabaseLoader`, which we've made elsewhere in our program, to get the user's details from a database. How `DatabaseLoader` works doesn't matter. What matters is that it's a 'service': it does something useful for us.

Now imagine that instead of loading information from a database, you want to get it from the filesystem. You have to do two things:

- Create a new service that gets the information you want from the filesystem.
- Change every part of your program that used the `DatabaseLoader` to now use a `FilesystemLoader`.

```csharp
public void ChangeUsername(int userID, string newName)
{
    var user = new FilesystemLoader.Load(id);
    user.Name = newName;
}
```

Changing your program like this isn't too annoying when you only have to change one line. But imagine that this is a real project, and you end up having to move lots of code over to the new `FilesystemLoader`. Now imagine that instead of the filesystem, you realise you need to get user information from [carrier pigeons](https://en.wikipedia.org/wiki/Homing_pigeon) instead.

```csharp
public void ChangeUsername(int userID, string newName)
{
    var user = CarrierPigeonLoader.Load(id);
    user.Name = newName;
}
```

All of these `Loader` classes have done the same thing - get user information. They did this in different ways, but in our `ChangeUsername` method, we don't care about how the service got the information - we just want the information. We need a way to express what we want out code to do (get a user's details) without specifying how it's done (database, filesystem, carrier pigeon).
If we can do this, it means we won't need to update `ChangeUsername` in the future if we come up with another way of completing this task.

This is what interfaces do.

## What is an interface?

An interface is a list of method signatures. Read the explanation on [delegates](delegates.md) for an explanation of method signatures.

Here is an interface with one method signature, named `Load`.

```csharp
interface ILoader
{
    public User Load(int id);
}
```

An interface looks similar to a class, with three differences:

- You use the `interface` keyword.
- You only define method signatures. You cannot define fields or method bodies in an interface.

(Advanced note: you can actually define [interface method bodies](https://www.infoq.com/articles/default-interface-methods-cs8/) from C# 8.0 onwards. You should not do this until you understand why the feature is available.)

## Implementing an interface

By themselves, interfaces don't do anything. Interfaces are not 'real' classes — not something concrete you can actually use. You can't use the `new` keyword to create an instance of an interface, for instance.

Interfaces exist so that actual classes can 'implement' them. When a class implements an interface, it says 'I agree to have methods that match the interface's method signature'.

```csharp
class CarrierPigeonLoader : ILoader
{
    // Without this method, your program will no longer compile.
    public User Load(int id)
    {
        // catch the bird, read the message on its leg...
    }
}
```

`ILoader` defines a method called 'Load' that takes an `int` and returns a `User`.
Because `CarrierPigeonLoader` implements `ILoader`, it has to have a method that matches that signature.

## Fixing the original problem

Imagine that you modified `DatabaseLoader`, `FilesystemLoader` and `CarrierPigeonLoader` to all implement `ILoader`.

We now know that whenever we use *any* of those classes, they **will** have a method named 'Load' that takes an `int` and returns a `User`. They will likely do this in different ways. That's OK. All we care about is the input (our `int`) and what we get back (the `User`).

Let's modify our original method.

public void ChangeUsername(int userID, string newName)
{
    ILoader loader = new CarrierPigeonLoader();
    var user = loader.Load(id);
    user.Name = newName;
}

When we declare a variable that has an interface as its type, it means 'I can be any of the classes that implement me'. If you are familiar with polymorphism, think of how a parent class can stand in for any class that inherits from it. In the second line, where we call `loader.Load()`, `loader` could be any class that implements our interface.

## Dependency injection

So far, this likely seems quite pointless. We have done a lot of extra work for nothing. We've created an interface, but we still say `new CarrierPigeonLoader()` in our method. We would have to update that every time we wanted to get user information in a new way.

The magic trick which makes interfaces actually useful is [dependency injection](dependency-injection.md).

public void ChangeUsername(ILoader loader, int userID, string newName)
{
    var user = loader.Load(id);
    user.Name = newName;
}

Our method no longer has any idea what `loader` 'really' is. As a result, we wouldn't have to update this method if we created a `MorseCodeLoader` or a `SignLanguageLoader`. Whatever code that calls `ChangeUsername` can swap out different implementations of `ILoader`, and the method won't notice that anything's changed.

But this still isn't great. Now the code that calls `ChangeUsername` has to be updated instead.

It is good by itself for lower-level parts of our programs to use interfaces and higher-level parts deal with concrete types. But if we use a DI framework (as I explain in the page on [dependency injection](dependency-injection.md)), we can centralise all of our service-creation in just one place. This means we could change our implementation of `ILoader` throughout our entire program by updating a few lines of code in just one place.

## When to use interfaces

You should consider creating an interface when you need to call on another class to get something done, but you don't care how it does it.

## Where to go from here

You will end up working with interfaces a lot in .NET, if only due to LINQ. Understanding interfaces will make the question 'what is the difference between `IEnumerable<>` and `List<>`?' a lot easier to understand.
