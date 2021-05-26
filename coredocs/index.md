# Simple explanations for .NET concepts

This site is a place for me to record my understanding of .NET, C# and related technologies. My hope is that it will also serve as a useful learning resources for anyone who was in the same position as me -- interested in .NET as a general concept but confused by unfamiliar names, project types and terminology.

My goal is to give you the 'eureka moment' needed to appreciate more detailed resources, like the [MSDN](https://docs.microsoft.com/en-us/dotnet/) or a [Manning Press](https://www.manning.com/) book. I try to write as directly and clearly as possible. If you read a page and think 'well, that's pretty simple, why did he feel the need to explain that?' -- I'm happy.

This page isn't a programming tutorial, so I won't explain fundamental concepts like variables or for-loops.

## Topics
* C#
    * [Interfaces](interfaces.md)
    * [Generics](generics.md) (incomplete!)
    * [The 'static' keyword](static.md)
    * [Delegates](delegates.md)
      * [Func<> and Action<>](func-and-action.md)
      * Lambdas
      * [Events](events.md)
    * [Async/await](async-await.md)
    * [Properties](properties.md)
* .NET project types
    * [Desktop](desktop-project-types.md)
    * [Web](web-project-types.md)
* The .NET runtime
    * [How your source code turns into a program](source-to-execution.md)
    * [Reference types and value types](reference-types-and-value-types.md)
    * [The garbage collector](garbage-collector.md)
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
* Yu Zhang for the lovely [Markdown All in One extension](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) that makes writing these pages easy and fun.
* Github Pages for hosting this site.
* Microsoft for developing .NET.
