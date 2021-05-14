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

Async/await is hard to learn about for a few reasons.

* Asynchronicity is inherently complicated.
* The terminology is often ambiguous.
* Because the terminology is ambiguous, it's hard to tell what specific problem async/await solves.

### Terminology 
In programming, it is very hard to find clear-cut definitions of words like 'asynchronous', 'concurrent' or 'parallel'. All of these terms are about *multiple things happening at once*, but they express it in different ways. Because people use these words differently, you should focus on the ideas that the words represent, rather than the words themselves.

### Threads
When you learn about asynchronicity, you will eventually hear about threads.

Many beginners think that async/await is 'really' just a convenient way to work with threads. This is not accurate. Async/await has no simple relation to threads.

Very often, in fact, [there is no thread at all](https://blog.stephencleary.com/2013/11/there-is-no-thread.html).

You should still learn about threads and the threadpool at some point, because they are a part of C# and the operating system you're using. But you should ignore them while learning about async/await. **Async/await has no simple relation to threads.**

### The problem async/await solves
When you say you want part of a program to be asynchronous, there are usually two things you can mean.

1. You have several operations that you want to run at the same time so that you can get through them all faster.
2. You have a slow operation (like writing to a file or downloading a webpage) that you want to run in the background and let you know when it's finished.

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

1. Everything is normal on the first line. The `HttpClient` is created and then assigned to the `client` variable.
2. On the second line, the program starts at the end and works backwards.
3. Your program calls the `GetAsync` method.
4. Your program sees the `await` keyword.
5. Your program immediately jumps out of `DownloadGoogleAsync` to whereever it was before.
6. `DownloadGoogleAsync` is 'frozen' in place as your program does work elsewhere.
7. Eventually, the download finishes. 
8. Your program immediately jumps back into `DownloadGoogleAsync` where it left off.
9.  Your program assigns the result of the `GetAsync` method to the `html` variable.
10. The final line is executed as normal.

## Questions
You should have several questions. 
* What does the `async` keyword do?
* Why is the return-type of the method `Task<string>`, and not just `string`? 
* What does 'the program jumps back to whereever it was before' mean?

### The async keyword
The only thing the async keyword does is let you use the `await` keyword inside that method. If you use an `await` statement in a method that isn't `async`, your program won't compile.

(Advanced note: `async` also tells the compiler to generate a state-machine for your method. But without any `await` statements you won't notice any difference.)

We need the `async` keyword for historical reasons. Before async/await was invented, people might have used variables called 'await' in their code. Microsoft didn't want to break existing code, so you have to opt-in to async/await by using the `async` keyword.

### Task<>
Normal C# methods return either `void` or some value (`string`, `int`, `MyObject`).
Async methods return either `Task` or `Task<>`. `Task<>` is generic, so you can have `Task<string>`, `Task<int>`, `Task<MyObject>`.

A `Task` represents an incomplete operation. You return a `Task` from an async method to indicate that the operation won't be finished right away.

Don't try to get the `int` out of something like a `Task<int>` directly. If you do, you'll make the method synchronous, defeating the point of using async/await.

You use the `await` keyword to 'complete' a `Task`. Consider this line from the example.

`var html = await client.GetAsync("www.google.com");`

The `GetAsync` method returns a [Task<HttpResponseMessage>](https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient.getasync?view=net-5.0). We use the `await` keyword to 'get' the `HttpResponseMessage` inside the task and assign it to a variable.

### The calling context
Understanding where your program 'goes' when it hits an `await` statement is complicated, and depends on the kind of program you're making. If your program has a GUI (like in Windows Forms or WPF), the program will go back to updating the UI. If you didn't use an async method for a long-running operation, the program's interface would 'freeze' until the operation was done.

## Integrating async into the rest of your program
Recall these three facts:

* An `async` method returns a `Task` or `Task<>`.
* You use the `await` keyword to 'complete' a `Task`.
* You can only use the `await` keyword in an `async` method.

These facts mean that async methods can only call, or be called by, other async methods. This makes integrating async code with the rest of your program difficult. This is called [the coloured functions problem](https://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/). As a result, you should think carefully before making a part of your program async.

There are two main solutions to this.

The first is to make your entire program asynchronous. If one method is async, everything is async. Since C# 7.1, [`Main()` can be async](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-7.1/async-main), making this cleaner to implement.

The second solution is to use async event handlers. (I will assume you know how events work for this section.)

I lied when I said that an `async` method can only return `Task` or `Task<>`. They can also return `void`. Generally, [you should not write `async void` methods](https://www.informit.com/articles/article.aspx?p=2832590&seqNum=2). Only use `async void` in event handlers. Here is an example:

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

We can now 'call' the `async` event handler by invoking an event in synchronous code. Making the event-handler for a UI element (such as the user clicking on a button) return async void is a common pattern.

## Making your own asynchronous functions
The easiest way to write an async function is to use the methods in C#'s standard library which are already async, like `HttpClient`'s `GetAsync`.

However, you will probably at some point want to make some already-existing synchronous method asynchronous.

You may try to do this by implementing a 'true' async method of your own -- not just one that slots together methods from the standard library. You should not attempt this. If you look at the source code for these methods, you will quickly find that it gets very complex and deals with raw calls out to the operating system. There is almost certainly a better way to solve your problem than to go down this road.

### Task.Run()
The `Task` class contains a static method, `Task.Run()`, which lets you essentially make any bit of code asynchronous. It does this by returning a `Task`, which you can `await`.

`Task.Run()` takes either an `Action` or a `Func<>` delegate, which you can read more about **here**.

Until I finish this section, I'm going to refer you to [this page on Pluralsight](https://www.pluralsight.com/guides/using-task-run-async-await) which is a fairly clear explanation of `Task.Run()`.