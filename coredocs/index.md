# Simple explanations for .NET concepts

## What is this site?
This site is a place for me to record my understanding of .NET, C# and related technologies. My hope is that it will also serve as a useful learning resources for anyone in the same position as me -- someone interested in .NET as a general concept but is confused bby the large amounts of unfamiliar names, project types and terminology.

My goal is to write in a direct and simple manner that gets you the 'essential core' of a topic without bogging you down in theoretical jargon or extraneous details. On the other hand, I'm going to try and avoid the kinds of wild and crazy metaphors you sometimes see in discourse about .NET, because I never found them useful for truly understanding a topic.

## What topics will you cover?
I plan to provide a broad overview of as much of .NET as I can. I do not aim to provide technical or authoritative explanations on any one topic. My hope is that a newcomer to the field can read my page on a topic that seems interesting to them, get a good rough understanding of what it's all about, and then jump into more formal sources (like Microsoft's official documentation or a Manning Press book) with confidence.

This is intended to cover topics which I think are commonly or easily misunderstood, so I won't explain fundamental programming concepts like variables or for-loops.

In no particular order, topics I'd like to cover include:
* C#
    * [Interfaces](interfaces.md) (incomplete!)
    * [Generics](generics.md) (incomplete!)
    * The 'static' keyword
    * [Delegates](delegates.md)
      * [Func<> and Action<>](func-and-action.md)
      * Lambdas
      * [Events](events.md)
    * [Async/await](async-await.md)
    * [Properties](properties.md)
* .NET project types
    * Desktop
      * Winforms
      * WPF
      * MAUI 
    * Web
      * Webforms
      * ASP.NET / ASP.NET Core
      * Blazor
      * SignalR 
* The .NET runtime
    * The CLR
    * The JIT
    * MSIL
* The developer experience
    * MSBuild
    * A tour of Visual Studio
* .NET as a concept
    * [The difference between .NET Framework, .NET Core, .NET 5/6](dotnet-versions.md)
    * The history of .NET
* Common .NET programming patterns
    * Logging
    * Unit testing
    * [Dependency injection](dependency-injection.md)

## Who is responsible for this?

My name is Jack Kendall. You can contact me through [the GitHub page for this site](https://github.com/jkendall327/dotnet-explanations). Feel free to make pull requests for any improvements (especially factual errors!).

## Credits and thanks

* Yu Zhang for the lovely Markdown All in One extension that makes writing these pages easy and fun.
* The HTML5 Boilerplate team for providing tools I will integrate at some point in this project.
* Github Pages for hosting this site.
* Microsoft for developing .NET.
