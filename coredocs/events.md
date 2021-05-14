# Events

[Home](index.md)

## In a nutshell

Events are [delegates](delegates.md) with a few extra safety features built-in. They are used to implement the 'observer' design pattern.

## The observer design pattern
We often want one part of our program to respond to something happening elsewhere in our program.

Imagine we have a counter that starts at zero and goes up by one every minute. When it hits ten, we want to print a celebratory message to the screen.

We could simply add a line of code in the counter that checks if it's at ten, then print the message.
But imagine we also want to save the current count to a file every time it's a prime number.
And that we want to shut down the user's PC when the count hits one hundred.

We could add all of this logic to the counter, but it would quickly get complicated and obscure what the counter is meant to do -- count.

A better model would be if our counter could 'send out' messages describing what its current count was. Then other parts of the program could do whatever they liked with that information, and it wouldn't affect the counter. It wouldn't even have to know who's listening to its messages. Think of it like a radio-tower sending out music to radios.

This is called the observer pattern, or the publisher-subscriber pattern.

You can implement this pattern with delegates. But they aren't the best choice for it in C#.

## Why delegates are bad for the observer design pattern
With delegates it is easy to break encapsulation and crash your program.

```
        // define a delegate
        public delegate void MyDelegate();

        // give a class an instance of the delegate
        public class MyClass
        {
            // our 'publisher'
            public MyDelegate ListOfMethods;
        }

        public class MyOtherClass
        {
            public void DoThingsBadly()
            {
                var myClass = new MyClass();

                // fill the delegate with 'subscribers' -- so far, so good
                // i can't be bothered writing multiple fake methods, so we'll just use the same one three times
                myClass.ListOfMethods += WriteMessage;
                myClass.ListOfMethods += WriteMessage;
                myClass.ListOfMethods += WriteMessage;

                // invoke the delegate
                // this will print "hello world" three times
                myClass.ListOfMethods();

                // uh-oh!
                myClass.ListOfMethods = null;

                // this will crash your program
                myClass.ListOfMethods();
            }

            // the example method we fill the delegate with
            // has to match the method signature of the delegate
            private void WriteMessage()
            {
                Console.WriteLine("hello world");
            }
        }
```

We should not be able to set the subscriber of another class to `null`. This doesn't just break things for `MyOtherClass`, but any part of the program that subscribed to `ListOfMethods`.
Instead of setting it to `null`, we could also clear the list of methods, set it to an entirely new list, or any number of other dangerous actions.
We also shouldn't be able to invoke the delegate whenever we want, either.

We need a special kind of delegate that can only be invoked or set in the class that created it. These are events.

## What events change

Events have the following restrictions.
* You can only directly assign values to them in the class where they were declared.
* Outside of the class where they were declared, you can only use the += and -= syntax to change their subscribers.
* You can only invoke an event in the class it was declared.

Here is the previous example rewritten to use events.

```
        // we still declare a method signature through a delegate
        public delegate void MyDelegate();

        public class MyClass
        {
            // the 'event' keyword is where the magic happens
            public event MyDelegate MyEvent;
        }

        public class MyOtherClass
        {
            public void DoThingsBadly()
            {
                var myClass = new MyClass();

                myClass.MyEvent += WriteMessage; // this is fine
                myClass.MyEvent += WriteMessage; // this is fine
                myClass.MyEvent += WriteMessage; // this is fine

                myClass.MyEvent(); // this breaks -- can't invoke an event from another class
                myClass.MyEvent = null; // this breaks -- can't set the value of an event from another class
            }

            private void WriteMessage()
            {
                Console.WriteLine("hello world");
            }
        }
```

Our program is now a lot safer. We can't break everything by accidentally setting a delegate to `null`.

However, it's still a bit ugly that we have to declare `MyDelegate`. Thankfully, there is a more convenient way to do things.

## EventHandler and EventArgs

In C# there is a very clearly-defined pattern for writing events. Much like how [Func<> and Action<>](func-and-action.md) were made as shorthand for common delegate use-cases, `EventHandler` and `EventArgs` were created for common event use-cases.

Here is the previous example re-written with these useful constructs.

```
        public class MyClass
        {
            // declares the event and its method signature in one line
            public event EventHandler MyEvent;
        }

        public class MyOtherClass
        {
            public void DoThingsBadly()
            {
                var myClass = new MyClass();

                myClass.MyEvent += WriteMessage;
            }

            // subscribers to the event now need to follow a special format
            private void WriteMessage(object sender, EventArgs e)
            {
                Console.WriteLine("hello world");
            }
        }
```
I've removed lines that wouldn't compile for simplicity.

`EventHandler` is a delegate that looks like this: `delegate void EventHandler(object sender, EventArgs e)`. It just saves us from declaring a separate delegate every time.

Returning `void` is simple. It doesn't make much sense for an event to return anything.

`object sender` is generally meant to be whatever class invoked the event. This lets subscribers query it for information.
`EventArgs` is a dummy class meant for carrying additional information. You typically use `EventArgs.Empty`, to represent no data being sent.

Here is what it would like like if `MyClass` wanted to invoke `MyEvent`.

```
        public class MyClass
        {
            public event EventHandler MyEvent;

            public void InvokeTheEvent()
            {
                MyEvent(this, EventArgs.Empty);
            }
        }
```

The `this` keyword passes in the current instance of `MyClass`. Because `EventHandler` asks for an `object`, you could, of course, send anything that you wanted.

## Passing along our own data

There are two ways for an event to carry along data to its subscribers. One, using a generic EventHandler, is simple but limited. The other, defining your EventArgs, is more involved but also more powerful.

### Generic EventHandler

The `EventHandler` class is generic, so we can have an `EventHandler<>` of any type. This lets us pass along a single value when our event is invoked.
`EventHandler` will always ask for an `object sender` along with the generic type.

```
        public class MyClass
        {
            // EventHandler now wants a string as well as an object
            public event EventHandler<string> MyEvent;

            public void InvokeTheEvent()
            {
                // pass in an object (this class) and a string
                MyEvent(this, "hello");
            }
        }

        public class MyOtherClass
        {
            public void DoThingsBadly()
            {
                var myClass = new MyClass();

                myClass.MyEvent += WriteMessage;
            }

            // subscribers still need to match the method signature of the EventHandler
            private void WriteMessage(object sender, string text)
            {
                // subscribers can now access information from the event
                Console.WriteLine(text);
            }
        }
```

This approach is good when you only want to send out simple information, like a number or a name. But if you want to send out more than one value, you need to define your own EventArgs.

### Defining your own EventArgs

To define your own event arguments, inherit from `EventArgs`.

```
        public class PersonEventArgs : EventArgs
        {
            string Name;
            int Age;
            bool IsAProgrammer;

            public PersonEventArgs(string name, int age, bool isAProgrammer)
            {
                Name = name;
                Age = age;
                IsAProgrammer = isAProgrammer;
            }
        }
```

It's convention to have *EventArgs* at the end of the name of your new class.
You typically define a constructor to make it easier to get information into the class when you're invoking your event:

```
            public void InvokeTheEvent()
            {
                MyEvent(this, new PersonEventArgs("Jack", 24, true));
            }
```

Subscribers can now access this information.

```
            private void WriteMessage(object sender, PersonEventArgs e)
            {
                // subscribers can now access information from the event
                Console.WriteLine("The name is:" + e.Name);
                Console.WriteLine("The age is:" + e.Age);
                Console.WriteLine("Are they a programmer? " + e.IsAProgrammer);

            }
```