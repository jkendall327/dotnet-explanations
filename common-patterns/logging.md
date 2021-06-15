# Logging - [Home](../index.md)

- [Logging - Home](#logging---home)
  - [In a nutshell](#in-a-nutshell)
  - [Why use logging?](#why-use-logging)
  - [An example](#an-example)

## In a nutshell

Logging is when your program records messages during runtime to show what it's doing.

## Why use logging?

Programs are complicated. We use several design patterns and coding styles to manage this, but there is a basic amount of complexity that we can't get rid of.

As a result, it is very difficult to understand what a program is doing at any given moment through logic alone. The gap between what we think a program does and what it actually does is the cause of almost all bugs.

Logging is a way for programs to tell us what they are doing.

## An example

The simplest form of logging is to write messages to the console, or 'standard output', when the program runs.

```csharp
public int MyMethod(int x)
{
    Console.WriteLine("The value of the argument is: " + x);

    x = x * x;

    Console.WriteLine("The value of the argument squared is: " + x);

    return x;
}
```

This is a form of logging.
