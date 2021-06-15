# Func<> and Action<> - [Home](index.md)

- [Func<> and Action<> - Home](#func-and-action---home)
  - [In a nutshell](#in-a-nutshell)
  - [The problem that `Func<>` and `Action<>` solve](#the-problem-that-func-and-action-solve)
  - [Action<>](#action)
  - [Func<>](#func)
  - [Predicate<>](#predicate)

## In a nutshell

`Func<>` and `Action<>` are convenient shortcuts for common [delegates](delegates.md) use-cases.
`Func<>` is for methods that return a value. `Action<>` is for methods that return void. 

## The problem that `Func<>` and `Action<>` solve

Delegates are a very powerful tool that let us put methods into variables and pass them to other methods as arguments.

The issue is that the syntax for delegates is quite heavy:

```
delegate void MyDelegate(string message);

public class MyClass
{
   public void UseADelegate()
   {
      MyDelegate messageTarget = WriteMessage;
      messageTarget("Hello, World!");
   }

   private void WriteMessage(string message)
   {
      Console.WriteLine(message);
   }
}
```

For this very simple example, we have to
* Define the method signatures the delegate accepts. (The `delegate void MyDelegate...` line)
* Create an instance of the delegate.
* Assign a method to the delegate. 
* Invoke the delegate.

`Func<>` and `Action<>` remove the first step by pre-defining a method signature for us. 

## Action<>
`Action<string>` is the same as `delegate void DelegateName(string argumentName)`.
Because `WriteMessage` returns `void`, we use `Action<>` rather than `Func<>`.

```
public class MyClass
{
   public void UseADelegate()
   {
      Action<string> messageTarget = WriteMessage;
      messageTarget("Hello, World!");
   }

   private void WriteMessage(string message)
   {
      Console.WriteLine(message);
   }
}
```

If you need more than one parameter, you just write them between the angle brackets.

```
public class MyClass
{
   public void UseADelegate()
   {
      Action<string, bool> messageTarget = WriteMessage;
      messageTarget("Hello, World!", true);
   }

   private void WriteMessage(string message, bool shouldWrite)
   {
    if (!shouldWrite) return;

      Console.WriteLine(message);
   }
}
```

You can [go up to sixteen arguments](https://docs.microsoft.com/en-us/dotnet/api/system.action-16?view=net-5.0) this way. (But if you ever get that high, you should probably do things differently.)

## Func<>
`Func<>` is like `Action<>` for methods that return a value. The final generic argument is the result.

```
public class MyClass
{
   public void UseADelegate()
   {
       // bool is the final generic argument
      Func<string, bool> messageTarget = WriteMessage;

      // and so messageTarget returns a bool
      var result = messageTarget("Hello, World!");
   }

   // messageTarget takes methods that accept a string and return a bool
   private bool WriteMessage(string message)
   {
      Console.WriteLine(message);
      return true;
   }
}
```

Again, you can [go up to sixteen arguments this way](https://docs.microsoft.com/en-us/dotnet/api/system.func-17?view=net-5.0), not counting the result.

## Predicate<>

There is also a `Predicate<>`, but I don't understand it well enough to explain it here :)
It's mainly used in LINQ.