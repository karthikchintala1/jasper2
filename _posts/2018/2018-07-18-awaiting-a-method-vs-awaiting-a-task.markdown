---
layout: "post"
title: "Awaiting A Method Vs Awaiting A Task"
date: "2018-07-18 10:11"
tags: async-await awaitables C# Task
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
cover: 'assets/covers/awaiting.jpg'
---
Most developers just await on methods instead of awaiting on Tasks returned by the awaiter. An awaiter is something that is returned by the method that is awaited.
In this post, we'll examine what happens when we await on methods instead of a task when we have multiple awaits in the method.

### Use Case:

We've two awaitable methods in the calling code which can process individually. This means the second awaitable call can happen while the first await is happening.

It's little confusing with all the awaiters and awaitables. If you don't understand the statement, read it again.

### Let's Code

For the purpose of this article, I'll test this in a Console application. Here goes our AwaitTest class.

```csharp
public class AwaitTest  
{  
    public async Task DoSomeThingAsync()  
    {  
        var time = Stopwatch.StartNew();  

        //a database call which takes 5 seconds to run  
        await Task.Delay(5000);  
        await PerformCalculationsAsync();  

        Console.WriteLine("time taken : " + time.Elapsed);  
    }  

    private async Task<int> PerformCalculationsAsync()  
    {  
        await Task.Delay(2000); //This can be some operation which takes 2 sec.  
        return 30;  
    }  
}
```
So, our DoSomethingAsync() has two awaitables here. One that takes 5 seconds to execute and the other takes 2 seconds to execute. Now, we'll call this in the Main method of our app and see how this goes.

Here is how we call our async method in the Main method of the application.

```csharp
public class Program  
{  
    static void Main(string[] args)  
    {  
        AwaitTest at = new AwaitTest();  
        at.DoSomeThingAsync().GetAwaiter().GetResult();  
    }  

}
```
With the above code in DoSomeThingAsync() method, can you guess the timing of the operation?
Well, the time taken for DoSomeThingAsync() to complete the operation is

> Time taken : 00:00:07.0079634

### So, what happened when we've awaited on the methods?

It's essentially what it is but we need to understand how it works. So, when a method is awaited it waits for the method to complete its operation.
Our first awaiter in the DoSomeThingAsync() method

> await Task.Delay(5000);

The above line just waits for 5 seconds to complete its operation. After it has completed its operation the next awaiter will be executed.

_await PerformCalculations();_ The above line takes 2 seconds to complete its operation. So the complete time for this operation is 07.0079634.

I'd recommend trying this by yourself just place a debugger in DoSomeThingAsync() method and watch what it is doing.

Now with this above approach, it just looks like synchronous operation as only one thing is executed at a time. So, we don't get the benefit of async-await here.

### Ok, what would have been better?

Instead of awaiting the method directly, we'll await on the Task instead of the methods.

I'll modify the DoSomeThingAsync() method here so that we'll await on Task

```csharp
public async Task DoSomeThingAsync()  
{  
    var time = Stopwatch.StartNew();  

    //a database call which takes 5 seconds to run  
    //await Task.Delay(5000);  
    //await PerformCalculationsAsync();  

    Task t1 = Task.Delay(5000);  
    Task t2 = PerformCalculationsAsync();  

    await t1;  
    await t2;  

    Console.WriteLine("time taken : " + time.Elapsed);  
}  
```
Now, guess the timing. Here's the output,

> time taken : 00:00:05.0080029

What changed the timing now?

Let's see what happened in the code.

```
Task t1 = Task.Delay(5000);  
Task t2 = PerformCalculationsAsync();
```

The above two lines just return immediately when debugged (it doesn't mean that the operation of those two is not completed). Instead, t1 and t2 will have their own Tasks returned.

```
await t1;  
await t2;  
```

When the first await t1; is executed it returns a Task and then the second await t2; will begin its execution. So, by the time the first task is completed the second task will be completed as the second task takes 2 seconds while the first task takes 5 seconds to complete.

So, our total time taken for execution will be 5 seconds.

### Take away

If we have multiple awaiting methods to execute in the method it's better to have them as Tasks instead of awaiting for one. Consider sorting the order of execution of the methods so that we could save some time for the other operation.

It could be just 2 seconds in this article but in real world cases 2 seconds is a huge time and there will be a performance consideration.


Alternatively, you could use Task.WhenAll method to wait for all the awaitable's to complete its operation.

Thanks for reading.
