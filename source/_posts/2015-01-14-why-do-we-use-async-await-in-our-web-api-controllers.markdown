---
layout: post
title: "Why you should use async/await in your Web API controllers?"
date: 2015-01-14 05:41:00 +0000
comments: true
categories: 
---
These days it seems as though all the code you write has to be asynchronous. It's like if it's not asynchronous, it must be wrong.

But what is the reason for this obsession?

This post examines the advantages using async/await in your Web API controllers.

###Threads are not cheap
The bottom line is - threads are expensive. They use up a fair amount of memory and too many of them will hurt the performance of your app.

Context switching - moving one thread out of a CPU core and putting another one in - is an expensive process. Thread data is copied from the CPU cache to RAM and vice-versa. This takes both time and memory.

The speed of the garbage collector will also be influenced by the number of threads in the process. When the garbage collector kicks in, it has to pause every thread and walk it's execution stack. The more threads you have, the more expensive this process is.

The optimum number of threads to have running on one machine is equal to the number of cores in the processor.

###Too many threads and the application doesn't scale
Asp.Net is a multi-threaded environment. Every new request is served on a different thread.

That is, every time a http request comes in, the thread pool checks if there is a thread available. If there isn't, it will have no choice but to create one.

For an app to perform optimally, it needs to server as many requests as it can, using as few threads as possible.

###Synchronous IO is bad
If your application performs IO synchronously, the executing thread becomes blocked. It just sits there wasting space, twiddling it's thumbs until the IO is complete.

Whilst this thread is waiting, another request may mean a new thread has to be created.

A new thread will use up more memory, cause more context switches, and slow garbage collection down.

What if that thread then blocks?

Another thread - using even more memory, causing even more context switches, and slowing garbage collection even further is created - so the cycle continues.

An Asp.Net application written in this manner will keel over with relatively few requests.

###Asynchronous IO is good
When IO is executed asynchronously, the thread is realesed when the operation is handed off to the hardware. This means that instead of blocking, the thread can be reused. 

What you need to do, is construct an **IHttpActionResult** for your controller to use. You can find out how [here](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/action-results).

Then, in the ExecuteAsync() method of your IHttpActionResult, perform IO using a method that ends in **async**. So, for instance, if you're using Entity Framework you might call **ToListAsync()** or **SumAsync()**.

Do this and your app will handle load far better than it otherwise would have.

###Summary
If you want your Web API application to scale, make sure you are writing asynchronous IO code. Pay attention when you're code:

- Reads or writes to a file
- Makes a database call
- Performs a web request

If the API you are using has a version of the method you need eding in **async** - USE IT!

