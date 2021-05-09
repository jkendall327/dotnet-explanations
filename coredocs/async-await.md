# Async/await

[Home](index.md)

## In a nutshell

Async/await is a convenient syntax for letting your program jump out of a method while it's doing something that takes a long time (like opening a file or downloading a webpage) and then jumping back into that method once the task is completed. Here is an example of async/await:

```
        public async Task<string> DownloadGoogleAsync()
        {
            var client = new HttpClient();
            var html = await client.GetAsync("www.google.com");
            return html.ToString();
        }
```

(Advanced note: you shouldn't use `HttpClient` like this. This is just an example.)

## Warning

Async/await is probably the most difficult concept to grasp in 'regular' C# (except maybe [generic coinvariance/contravariance](https://docs.microsoft.com/en-us/dotnet/standard/generics/covariance-and-contravariance), which is much less important).

It's hard to learn about for a few reasons.

* Asynchronicity is inherently complicated.
* The terminology is often ambiguous.
* It's hard to tell how other topics, like threads and the Task Parallel Library, are related to async/await.

### Threads

When you learn about async/await and asynchronicity, you will eventually hear about threads.

Threads are important to know about, but you should ignore them while learning about async/await. Thinking that async/await is 'really' just about threads will give you the wrong idea about how it works. Async/await has no simple relation to threads.

## What is asynchronicity?

In programming, it is very hard to find a clear-cut definition of words like 'asynchronous', 'concurrent' or 'parallel'. All of these terms are about *multiple things happening at once*, but they express it in different ways. People use these words differently, so it's important to focus on the ideas that the words represent, rather than the words themselves.

There are two main kinds of asynchronicity to worry about.

1. You have several operations (like complicated mathematical equations) that you want to run at the same time so they finish faster.
2. You have a long-running operation (like opening a file or downloading a webpage) that you want to run in the background and let you know when it's finished.

Async/await is desgined for the second kind of asynchronicity. For the first, you want the [Task Parallel Library](https://docs.microsoft.com/en-us/dotnet/standard/parallel-programming/task-parallel-library-tpl).

## What happens when an async method runs

Let's track what happens when you run an `async` method.

```
        public async Task<string> DownloadGoogleAsync()
        {
            var client = new HttpClient();
            var html = await client.GetAsync("www.google.com");
            return html.ToString();
        }
```

When your program runs this method, the following things will happen.

1. At first, everything is normal. The `HttpClient` is created.
2. Your program jumps to the end of the second line and calls the `GetAsync` method.
3. Your program starts working backwards through the second line, and sees the `await` keyword.
4. Your program immediately jumps out of `DownloadGoogleAsync` to whatever method it was in before.
5. Your program continues as normal going through whatever method it was in before `DownloadGoogleAsync`.
6. Eventually, the file download finishes. 
7. Your program jumps back into `DownloadGoogleAsync` where it left off.
8. Your program assigns the result of the `GetAsync` method to the `html` variable.
9. The `return` statement is executed as normal.

## Questions

You should still have several questions. What does the `async` keyword do? Why is the return-type of the method `Task<string>`, and not just `string`? What does 'the program jumps back to whatever method it was in before' mean?

Let's answer those questions one by one.

### The async keyword

The only thing the async keyword does is let you use the `await` keyword inside that method. If you use an `await` statement in a method that isn't `async`, the compiler will report an error.

(Advanced note: `async` also tells the compiler to generate a state-machine for your method. But without any `await` statements you won't notice any difference.)

You might wonder why we need this `async` keyword at all. The answer is historical. Before async/await was invented, people might have used variables called 'await' in their code. Microsoft didn't want to break existing code, so you have to explicitly opt-in to async/await with `async`.

### Task<>

Normal C# methods return either `void` or some value (`string`, `int`, an instance of a class, etc.).
Async methods return either `Task` or `Task<>`. `Task<>` is generic, so you can have `Task<string>`, `Task<int>`, `Task<MyObject>`, etc.

`Task`s represent an operation that isn't complete yet. You return a `Task` from an async method to indicate that the operation won't be complete right away.

You can't get the value out of a `Task<>` directly. If you try, you'll make the program synchronous, defeating the point of using async/await.

You use the `await` keyword to 'complete' a `Task`. Consider this line from the example.

`var html = await client.GetAsync("www.google.com");`

The `GetAsync` method returns a [Task<HttpResponseMessage>](https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient.getasync?view=net-5.0). We use the `await` keyword to 'get' the `HttpResponseMessage` inside the task and assign it to a variable.

### The calling context
Understanding where your program 'goes' when it hits an `await` statement is complicated, and depends on the kind of program you're making. If your program has a GUI (like in Windows Forms or WPF), the program will go back to updating the UI and listening for user input. If you didn't use an async method for something like downloading a file, the program's interface would 'freeze' and not respond until the file had downloaded.

## Async all the way down

Recall these three facts:

* An `async` method returns a `Task` of some kind.
* You use the `await` keyword to 'complete' a `Task`.
* You can only use the `await` keyword in an `async` method.

Combined, these facts mean that async methods can only call (or be called by) other async methods. This makes integrating async code with the rest of your program difficult. There are two main solutions that I know about.

https://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/

The first is just making your entire program asynchronous. If one bit of code is async, everything has to be async. Since C# 7.1, [`Main` can be async](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-7.1/async-main), making this possible.

The second solution is to use async event handlers. (I will assume you know how events work for this section.) I lied, earlier, when I said that an `async` method only returns `Task` or `Task<>`. They can also return `void`. There are several good reasons why you should not write `async void` methods most of the time. The only time where it is a good idea is in event handlers, because event handlers only ever return void.

```
        public async Task<string> DownloadGoogleAsync()
        {
            var client = new HttpClient();
            var html = await client.GetAsync("www.google.com");
            return html.ToString();
        }

        public event EventHandler MyEvent;

        public void Setup()
        {
            MyEvent += MyAsyncEventHandler;
            MyEvent.Invoke(this, EventArgs.Empty);
        }

        private async void MyAsyncEventHandler(object sender, EventArgs e)
        {
            await DownloadGoogleAsync();
        }
```

We can now 'call' the `async` event handler by invoking an event in synchronous code. This works because events are inherently async. This approach only really works for 'fire-and-forget' scenarios.