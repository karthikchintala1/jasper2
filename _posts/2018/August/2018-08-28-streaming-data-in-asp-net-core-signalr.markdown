---
layout: "post"
title: "streaming data in asp.net core signalr"
date: "2018-08-28 06:11"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: ["ASP.NET Core", "SignalR", "Streaming"]
cover: 'assets/covers/'
description: This is a tutorial for streaming in ASP.NET CORE SignalR.
---

In this post we'll see how to stream the data in ASP.NET Core SignalR. With ASP.NET  Core 2.1 released, SignalR now supports streaming content.

### What is a [Stream](https://www.webopedia.com/TERM/S/streaming.html)?

> Streaming or media streaming is a technique for transferring data so that it can be processed as a steady and continuous stream. -- webopedia.com

### When to stream?

In the scenarios where the data will with some latency from the server and we don't have to wait for the content to arrive. For this scenario we can use data streaming.

This can also be useful when we don't want to get all the data at a time as this might be time consuming. So, we'll send the data in chunks/fragments from server to client. 

---

Now, we'll see how to setup the `ASP.NET Core SignalR` in Visual Studio.

### Creating ASP.NET CORE Application

It's always the same thing Go over to `File` >> `New Project` and give it a name.

![streaming data in asp.net core signalr](C:\Users\K\Desktop\signalrstream\New_Project_SignalRStream.png)

And now select `Web Application` from the templates and your framework as `ASP.NET Core 2.1`

![streaming data in asp.net core signalr](C:\Users\K\Desktop\signalrstream\web_app.png)

Once you're done with that you should have your project in the solution folder.

We'll start writing our Hubs now.

### Hub setup

Unlike normal signalR methods, the stream methods will be different as they have stream the content over time when the chunks of data is available.

Create a C# file in the project with the name as `StreamHub` or whatever. It is better to add it in a Folder though.

Derive that from `Hub` class and add a namespace in the file as `using Microsoft.AspNetCore.SignalR;`

Now, create a method in the class with the return type as `ChannelReader<T>` here T is the type of the value returned. The `ChannelReader` return type on a method makes a streaming hub method. Here is the code for streaming our data.

```csharp
public ChannelReader<int> DelayCounter(int delay)
        {
            var channel = Channel.CreateUnbounded<int>();

            _ = WriteItems(channel.Writer, 20, delay);

            return channel.Reader;
        }

        private async Task WriteItems(ChannelWriter<int> writer, int count, int delay)
        {
            for (var i = 0; i < count; i++)
            {
                //For every 5 items streamed, add twice the delay
                if (i % 5 == 0)
                    delay = delay * 2;

                await writer.WriteAsync(i);
                await Task.Delay(delay);
            }

            writer.TryComplete();
        }
```

- `DelayCounter` is our streaming method, this takes a delay parameter to specify from the client end.
- `WriteItems` is a private method and this returns a `Task`. 
- The last line in the `WriteItems` is `.TryComplete()` on the stream says stream is completed and is closed to the client.

### Configuring SignalR in the project

1. Head over to the `Startup` class and locate `ConfigureServices` method and add the following line at the end (skip this if you can configure yourself)

   ```csharp
   services.AddSignalR();
   ```

2. We also need to add route for signalR stream. Now, head over to the `Configure` method in the Startup class and add the following

   ```csharp
   app.UseSignalR(routes =>
               {
                   routes.MapHub<ChatHub>("/streamHub");
               });
   ```

### Add SignalR client library

This is to add the signalR js on the client side.

Launch **Package Manager Console** (PMC) from the Visual Studio and navigate to project folder with the following command

> cd CodeRethinked.SignalRStreaming

Run npm init to create package.json file

> npm init -y

Ignore the warnings. Install the signalr client library

> npm install @aspnet/signalr

The npm install downloads the signalr client library to a sub folder under `node_modules` folder.

### Copy the signalr from node_modules

or use libman.json