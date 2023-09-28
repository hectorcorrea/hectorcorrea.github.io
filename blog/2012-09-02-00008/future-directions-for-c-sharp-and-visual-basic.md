# Future Directions for C# and Visual Basic
These are my notes from Anders Hejlsberg session on Future Directions for C# and Visual Basic.

It's been more than 10 years since managed code was introduced back in PDC 2000.  Every new release of C# has had one or two major themes:

* Version 1: Managed Code was introduced
* Version 2 2: Generics
* Version 3: LINQ
* Version 4: Dynamic features and language parity

The two major themes in the next release (version 5) are support for **WinRT**in Windows 8 and **asynchronous programming**.

Asynchronous Programming is becoming the norm in modern, connected applications.

About 10-15% of the WinRT API supports asynchronous operations. Sounds like a small percent but this percent is probably the most used stuff.

There are different asynchronous programming models in Windows 8 and .NET

* WinRT uses AsyncOperation<T>
* .NET uses Task<T>
* JavaScript uses Promises

All of these models are objects representing "ongoing operations". 

Callbacks turn your code inside out and are a big challenge but they have figured out how to automatically handle this so that asynchronous
code "feel just like good old synchronous code". The general pattern is:

```
public async SomeMethodAsync()
{
  // code before firing async operation
  await code to execute async
  // code to execute after async operation has completed
}
```

Use `await` operator to cooperatively yield control.

You can use `Task.WhenAll()` to run multiple tasks asynchronous in parallel!

It turns out that asynchronous method don't start new threads! I need to do more reading on this. Threading is only need when you run CPU intensive operations like a loop to perform math calculations.

Anders showed some JavaScript in which he consumed some C# code from within C# but he couldn't just use Task as-is and he had to bridge
the two through `IAsynchOperation` (didn't quite get why) 

Anders is obviously a big proponent of static languages and took several jabs at the dynamic nature of JavaScript. The funniest line was when he said:

> “Sometimes you get lucky in JavaScript, you always get lucky in C#”

Another feature that is coming in C# is what is called Caller Info Attributes. These are built-in attributes that you can add to parameters to a method to
receive the name of the caller file path, the caller line number and member name.

* [CallerFilePath]
* [CallerLineNumber]
* [Caller/MemberName]

This is very nice for logging code.

## Roslyn Project

Towards the end of the session Anders talked about what’s coming in C# 6.0 and a project named “The Roslyn project.” The basics of this project are:

* Reimagine what compilers do
* Compiler as a service
* Open the compiler, make its information available to others (formatters, highlighters, refactoring, DSL)

With feature the syntax tree produced by the C# compiler becomes available to other tools so that they can peek inside the code with a true semantic knowledge of what the code does.

Anders shows a C# interactive prompt in which he was able to create a C# program on the fly, line by line, and execute each line as it went. This is nothing new to people that use JavaScript or Ruby but for a static language like C# this is huge! I was really impress on how clean the syntax to do this was.

The following picture (blurry) shows an example of what he did. In this code he declares a square function in a string and executes is in the program via a delegate. Wicked stuff.

![Roslyn code](https://hectorcorrea.com/images/buildwin2.jpg")

Another example of using Roslyn that we showed was a feature to paste C# code as VB.NET code and vice-versa. This showed how the semantic of the original code was preserved.

There is a CTP available of Roslyn already.

The C# and VB.NET compilers are being rewritten in managed code.