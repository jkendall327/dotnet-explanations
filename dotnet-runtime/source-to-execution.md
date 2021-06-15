# How your source code turns into a program - [Home](../index.md)

- [How your source code turns into a program - Home](#how-your-source-code-turns-into-a-program---home)
  - [In a nutshell](#in-a-nutshell)
  - [The problem this solves](#the-problem-this-solves)
  - [The Common Language Runtime](#the-common-language-runtime)
  - [The JIT](#the-jit)

## In a nutshell

When you run a .NET program, it's like booting up a very small [virtual machine](https://en.wikipedia.org/wiki/Virtual_machine) made just for running your code.

## The problem this solves

The reason .NET works this way - by running your code in a small virtual machine - is because [Java works this way](https://en.wikipedia.org/wiki/Java_virtual_machine).

The main reason Java does it that way is for portability.

Different kinds of computers have different *architectures*, meaning their processors work with different sets of instructions.

If you want a program to run on different architectures, you need a compiler that 'targets' them. Targeting seven architectures means writing seven separate compilers, which is obviously not ideal.

Java responded to this by abstracting the machine your code runs on. Rather than running on any specific architecture, your code compiles to an imaginary architecture. This is usually called bytecode.

When a new kind of architecture comes out, you can now support it by just porting over the virtual machine. Existing code will work on new platforms 'for free', and won't have to be recompiled.

Here is the idea in simpler terms:

1. You write a program.
2. Your code is compiled to an 'intermediate' language.
3. When a user opens the program, the virtual machine turns the intermediate code into real machine instructions the computer can understand.

.NET works in a very similar way.

## The Common Language Runtime

The Common Language Runtime, also informally called the '.NET runtime', is the virtual machine that your C# code targets.

There is another advantage to using an intermediate language: multiple languages can compile down to it. In .NET, for instance, C# and F# both compile to CIL. As a result, you can think of those languages as 'really' just being versions of CIL that are easier to use. You can write CIL directly, if you want.

## The JIT

The Just-in-Time compiler is what turns CIL into real machine instructions when you run a .NET program.
