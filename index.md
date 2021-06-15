# Simple explanations for .NET concepts

This site is a place for me to record my understanding of .NET, C# and related technologies. My hope is that it will also serve as a useful learning resources for anyone who was in the same position as me — interested in .NET as a general concept but confused by unfamiliar names, project types and terminology.

My goal is to give you the 'eureka moment' needed to appreciate more detailed resources, like the [MSDN](https://docs.microsoft.com/en-us/dotnet/) or a [Manning Press](https://www.manning.com/) book. I try to write as directly and clearly as possible. If you read a page and think 'well, that's pretty simple, why did he feel the need to explain that?' — I'm happy.

This page isn't a programming tutorial, so I won't explain fundamental concepts like variables or for-loops.

## Topics

* C#
  * [Interfaces](csharp/interfaces.md)
  * [Generics](csharp/generics.md) [incomplete]
  * [The 'static' keyword](csharp/static.md) [99% complete]
  * [Delegates](csharp/delegates.md)
  * [Func<> and Action<>](csharp/func-and-action.md)
  * Lambdas
  * [Events](csharp/events.md)
  * [Async/await](csharp/async-await.md) [99% complete]
  * [Properties](csharp/properties.md)
  * [LINQ](csharp/linq.md)
* .NET project types
  * [Desktop](project-types/desktop-project-types.md) [incomplete]
  * [Web](project-types/web-project-types.md) [incomplete]
* The .NET runtime
  * [How your source code turns into a program](dotnet-runtime/source-to-execution.md) [incomplete]
  * [Reference types and value types](dotnet-runtime/reference-types-and-value-types.md)
  * [The garbage collector](dotnet-runtime/garbage-collector.md)
* The developer experience
  * MSBuild
  * [A tour of Visual Studio](developer-experience/vs-tour.md) [incomplete]
* .NET as a concept
  * [The difference between .NET Framework, .NET Core, .NET 5/6](general-dotnet/dotnet-versions.md) [incomplete]
  * The history of .NET
* Common .NET programming patterns
  * [Logging](common-patterns/logging.md)
  * [Unit testing](common-patterns/unit-testing.md)
  * [Dependency injection](common-patterns/dependency-injection.md)

## Who is responsible for this?

My name is Jack Kendall. You can contact me through [the GitHub page for this site](https://github.com/jkendall327/dotnet-explanations). Feel free to make pull requests for any improvements (especially factual errors!).

## Credits and thanks

* Yu Zhang for the lovely [Markdown All in One extension](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) that makes writing these pages easy and fun.
* Github Pages for hosting this site.
* Microsoft for developing .NET.
