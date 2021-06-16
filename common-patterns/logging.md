<!-- title: Logging -->
# Logging - [Home](../index.md)

- [Logging - Home](#logging---home)
  - [In a nutshell](#in-a-nutshell)
  - [Why use logging?](#why-use-logging)
  - [An example](#an-example)
  - [Solutions](#solutions)

## In a nutshell

Logging is when your program sends out messages to explain what it's doing.

## Why use logging?

Programs are complicated. Design patterns and clever coding styles reduce this, but programs will always be difficult to fully understand. The gap between what we think a program does and what it actually does is the cause of almost all bugs.

Logging is a way for programs to tell us what they are doing.

This can tell us what branches of execution our program hits, or doesn't. It can record business events, like when a user makes a purchase on an online store.

Logging gives us information that helps us analyze and debug programs.

## An example

The simplest form of logging is writing messages to the console.[^1]

```csharp
public int MyMethod(int x)
{
    Console.WriteLine("The value of the argument is: " + x);

    x = x * x;

    Console.WriteLine("The value of the argument squared is: " + x);

    return x;
}
```

This is simple to implement, but has flaws.

- It's not permanent. We lose this information when the program ends.
- It depends on having a console to write to.
- It's just flat text. There is no metadata to help us find interesting patterns in the logs.

## Solutions

For serious programs, *structured logging* is very useful. In contrast to using `Console.WriteLine`, structured logs aren't just text. They're usually [JSON](https://en.wikipedia.org/wiki/JSON), a format which lets us include metadata about the messages we're recording.

A popular way to get structured logging in C# is to use the [Serilog](https://serilog.net/) library.

[^1]: Also called 'standard output' or 'STDOUT'.
