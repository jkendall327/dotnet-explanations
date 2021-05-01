# Interfaces

[Home](intro.md)

## In a nutshell

Interfaces let you say what you want your code to do without specifying how it does it.

## Why care about interfaces?

Interfaces seem very abstract and unnecessary at first. They are, however, a very useful way to make your code more flexible and maintainable. (Buzzword counter: 2.)

Don't go looking for situations where you can use interfaces. If you use an interface in every single situation where you can, you will make your project more complicated for no reason. 

You will end up working with interfaces a lot in .NET, if only due to LINQ. Understanding what an interface is will make the age-old question 'what is the difference between `IEnumerable<>` and `List<>`?' a lot easier to understand.

## The problem

Interfaces are a solution to a problem. I will explain this problem to explain interfaces.

Imagine a program that lets people register user-accounts and edit their details. You might have a method that looks like this.

```
public void ChangeUsername(int userID, string newName)
{
    var user = UserDetailsDatabaseLoader.Load(id);
    user.Name = newName;
}
```

We use some kind of class named `UserDetailsDatabaseLoader` to get the user's details, presumably from a database. How `UserDetailsDatabaseLoader` works doesn't matter. The important idea is that it's a 'service': it does something useful for us. 

Now imagine that your boss comes into the office and says 'we're not using databases anymore; we're loading all user information from the filesystem'. OK, this won't be hard to change. 

```
public void ChangeUsername(int userID, string newName)
{
    var user = UserDetailsFilesystemLoader.Load(id);
    user.Name = newName;
}
```

We now have a different service, `UserDetailsFilesystemLoader`. It does the same thing (gets us a user's details) but does it in a different way (loading them from the filesystem, rather than a database).

Now imagine that your boss comes into the office and says 'we're not using the filesystem anymore; we're getting all user information from [carrier pigeons](https://en.wikipedia.org/wiki/Homing_pigeon)'. 

```
public void ChangeUsername(int userID, string newName)
{
    var user = UserDetailsHomingPigeonLoader.Load(id);
    user.Name = newName;
}
```

This is getting annoying. All of these `Loader`s have done the same thing - get user information. If there was a way to express just that idea, without specifying how it's done, we wouldn't have to change `ChangeUsername` in the future.

This is the problem that interfaces solve.

## The solution

An interface is a list of method signatures. If you don't know what a method signature is, I talk about them in the explanation on [delegates](delegates.md).

Here is an example of an interface. 

```
interface ILoader
{
    public User Load(int id);
}
```

An interface looks very similar to a class. There are two main differences:
* You use the `interface` keyword.
* You only define method signatures. You cannot define fields or method bodies in an interface.

(Advanced note: you can actually define [interface method bodies](https://www.infoq.com/articles/default-interface-methods-cs8/) from C# 8.0 onwards. You should not do this until you understand why the feature is available.)

By themselves, interfaces are useless. You can't use the `new` keyword to create an interface in your code.

Classes, however, can 'implement' an interface. The syntax for doing this is very similar to class-inheritance.

```
class UserDetailsHomingPigeonLoader : ILoader
{
    // class implementation...
}
```

When a class implements an interface, it has to include all of the interface's method signatures. Because `ILoader` defines a method called 'Load' that takes an `int` and returns a `User`, that means `UserDetailsHomingPigeonLoader` has to do the same thing.

```
class UserDetailsHomingPigeonLoader : ILoader
{
    // Without this method, your program will no longer compile.
    public User Load(int id)
    {
        // catch the bird, read the message on its leg...
    }
}
```

Imagine that you modified the other classes for getting user information in the same way, so that they all implemented `ILoader`.

We can now return to our original method and make an important change.

public void ChangeUsername(int userID, string newName)
{
    var user = ILoader.Load(id);
    user.Name = newName;
}