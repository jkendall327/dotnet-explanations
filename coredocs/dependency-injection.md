# Dependency injection

## IN A NUTSHELL

Dependency injection is when you pass stuff in through a class's constructor rather than creating that stuff in the class itself. Here is an example of dependency injection:

public class MyClass
{
	private readonly IHttpClientFactory _client;

	public MyClass(IHttpClientFactory client)
	{
		_client = client;
	}

	public void MyFunction()
	{
		_client.DownloadAsync("www.example.com");
	}
}

This is all that dependency injection is. The IHttpClientFactory is passed in through MyCLass's constructor, rather than the class creating a HttpClient of its own with the 'new' keyword.

If dependency injection ever starts feeling complicated, remember that at the end of the day it's nothing more than this. Lots of tools and patterns have been created to make doing this easier or more powerful, but everything relies on the basic idea of passing stuff in through a class's constructor.

## SO WHAT?

There are several reasons why doing this might be useful.

* MyCLass doesn't know where the IHttpClientFactory has come from. This is good, because it *shouldn't* know -- it's none of its concern. All MYClass should care about is that it has one.
* MyClass isn't cluttered up with lots of code for creating the IhttpClientFactory.
* You can see that MyClass requires an IHttpClientFactory just by looking at its constructor. You don't need to look through the class to see that. You won't be surprised by a sudden reference to it halfway through the file.
* Because MyCLass asks for the IHttpClientFactory in its constructor, it's impossible to create the class without giving it one. If there was a separate method for giving MyCLass the factory, you could forget to call it. If MyClass created the factory itself, it could do it wrong and mess everything up.

You can stop reading here and you would have a pretty good idea of what dependency injection is. 

## USING INTERFACES WITH DEPENDENCY INJECTION

It is very common to use interfaces with dependency injection. Rather than directly asking for an instance of a class in your constructor, you ask for an interface which represents this class.

In other words, MyClass asks for an IHttpClientFactory (an interface) rather than a HttpClient (a normal class).

This is common because it makes your code more flexzible. By using dependency injection, MyCLass doesn't know where its web-client comes from. By using an interface instead of a concrete class, MyClass also doesn't know what specific class it's using.

This means that we can swap out whatever we give MyClass with anything that implements the IHttpClientFactory. MyClass won't know the difference.

Using interfaces is not a key part of dependency injection, it's just a common combination of two useful ideas that work well together. Most of the benefits in this section are just because of using an interface, not specifically because you injected one.

## DI CONTAINERS

Because MyCLass asks for something in its constructor, we have to give it an instance of that thing whenever we create an instance of MyCLass. 

In essence, we have moved things up one level. Rather than MyClass creating an IHttpClientFactory itself, now whatever bit of code that creates a MyClass has to do it instead.

This might seem pointless by itself. The secret that makes DI really useful is that we can repeat this process.Whatever class that creates MyClass can simply ask for an IHttpClientFactory in *its* constructor. This process can spiral upwards and upwards until you eventually reach the top of your program (the 'entry-point'().

Now, you can create all of your classes in one place and simply feed them in to one another. When all of your object creation is in one place, it's much easier to see how different classes depend on each other.

It can be boring to manually pass instances of your classes into each other. Many people have created tools to automate this process, known as DI containers.

All a DI container does is sit at the start of your program, keep a big list of all the classes you're going to use, and feed them into each other.
