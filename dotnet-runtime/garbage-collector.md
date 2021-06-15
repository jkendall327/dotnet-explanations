<!-- title: The garbage collector -->
# The garbage collector - [Home](../index.md)

- [The garbage collector - Home](#the-garbage-collector---home)
  - [In a nutshell](#in-a-nutshell)
  - [Programs need memory](#programs-need-memory)
  - [The garbage collector in action](#the-garbage-collector-in-action)
    - [A myth about `null`](#a-myth-about-null)
  - [Other approaches to managing memory](#other-approaches-to-managing-memory)
  - [The stack and the heap](#the-stack-and-the-heap)
  - [Further reading](#further-reading)

## In a nutshell

The .NET garbage collector lets our programs act as if they have infinite memory to work with.

## Programs need memory

When you run a program, the operating system (Windows, Linux, Mac) controls how much memory it can access.

Imagine you write a program that only ever needs 20 megabytes of memory. If the operating system gives you 30 megabytes, well, everything is great. You don't need to worry about memory at all. You effectively have infinite memory.

The issue is if your program needs more memory than the operating system is willing to give you. If your program runs out of memory, it'll crash. To get around this, we ask the operating system for memory only when we need it, and then give it back when we're done with it.

The garbage collector automates this and makes it seem like we have infinite memory.

## The garbage collector in action

You almost never have to think about the garbage collector when you're writing a program. You should only think about it if performance is very important or in a few specific scenarios (like [concatenating strings](https://docs.microsoft.com/en-us/dotnet/framework/performance/performance-tips)).

You can think of the `new` keyword as invoking the garbage collector.

```csharp
public void MakeSomeObjects()
{
    var sb = new StringBuilder();
}
```

The `new` keyword tells the garbage collector (or more accurately, the .NET runtime) to ask the operating system for enough memory to hold a `StringBuilder`.

When it decides your program needs to give some memory back, the garbage collector will look through all the objects in your program. If there any objects that it can safely get rid of, it will deallocate that object's memory and give it back to the operating system.

What does 'safely get rid of' mean? [It's complicated](https://stackoverflow.com/questions/2257563/what-are-the-generations-in-garbage-collection), but essentially the garbage collector will check if any part of your program is currently using that object, or *could* use that object. It builds a big graph of all the objects in your program, and if any are left on their own, they can be safely removed.

### A myth about `null`

Some people think that when you're done with an object, you should set it to `null` so the garbage collector knows it's OK to collect it.

This is [pointless and potentially dangerous](https://stackoverflow.com/questions/3903878/c-should-object-variables-be-assigned-to-null). The point of the garbage collector is that you don't have to think about memory. Microsoft has spent a lot of money and  time on engineering the garbage collector - you don't need to help it along.

For the same reason, you also almost never need to call the `GC.Collect()` method to manually invoke the garbage collector.

## Other approaches to managing memory

.NET's garbage collector makes coding easier and less error-prone than other languages.

However:

- Garbage collection is relatively slow.
- You can't predict when garbage collection will happen.

As a result, garbage collection doesn't work well in embedded systems or other places where performance is critical.

There are two main alternatives to garbage collection:

- Manual memory management. You have to explicitly ask the operating system for memory and give it back when you're done with it. This is very fast but it's easy to make mistakes. Languages like C and C++ use this.
- A borrow-checker. [Rust](https://www.rust-lang.org/) allocates and deallocates memory for you, but you have to follow very strict rules to make it work. This is also fast.

None of these options are better or worse than using a garbage collector. There is a basic trade-off between convenience and reliability/speed. If you have a solution with the best of both worls, please let the world know :)

## The stack and the heap

The stack and the heap are not relevant to garbage collection. The garbage collector simulates a machine with infinite memory. Whether the program splits that memory into a heap and a stack is an implementation detail.

I highly recommend [Raymond Chen's article on the garbage collector](https://devblogs.microsoft.com/oldnewthing/20100809-00/?p=13203) for more on this.

## Further reading

Microsoft's [introduction to the garbage collector](https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals) is a good in-depth read.
