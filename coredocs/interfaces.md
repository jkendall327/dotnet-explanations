# Interfaces

[Home](intro.md)

## In a nutshell

Interfaces let you focus on what you want your code rather than how it does it.

## Why care about interfaces?

Interfaces seem very abstract and unnecessary at first. They are, however, a very useful way to make your code more flexible and maintainable. (Buzzword counter: 2.)

Don't go looking for situations where you can use interfaces. If you use an interface in every single situation where you can, you will make your project more complicated for no reason. 

You will end up working with interfaces a lot in .NET, if only due to LINQ. Understanding what an interface is will make the age-old question 'what is the difference between `IEnumerable<>` and `List<>`?' a lot easier to understand.

## Explanation

Interfaces are a solution to a problem. Imagine a program that lets people register user-accounts and edit their details. You might have a method that looks like this.

```
public void ChangeUsername(int userID, string newName)
{
    var user = UserDetailsDatabaseLoader.Load(id);
    user.Name = newName;
}
```

...

You then learn that you will also have to load user details from the filesystem, and not just a database.

```
public void ChangeUsername(int userID, string newName)
{
    var user = UserDetailsFilesystemLoader.Load(id);
    user.Name = newName;
}
```

Constantly going back and changing this code every time we decide on a different way to store user information is pretty annoying.

This method really shouldn't care about how it gets a user's information -- all it cares about is if it has the user or not.

Using an interface lets us express the 'idea' of something which gives us user details without specifying how it does it.