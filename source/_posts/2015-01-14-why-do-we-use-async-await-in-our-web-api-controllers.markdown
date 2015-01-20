---
layout: post
title: "Why do we use async await in our Web API controllers?"
date: 2015-01-14 05:41:00 +0000
comments: true
categories: 
---
These days it seems as though everything you write has to be asynchronous. It's as if everyone has asynchitis.

But what is the reason for this obsession?

This post examines the advantages using async/await in your controllers.

###Threads are not cheap
The bottom line is - threads are expensive. They use up a fair amount of memory and too many will hurt the performance of your app.

Context switching (moving one thread out of a core and putting another one in) is a slow process. As one thread is "switched" out, it's data is copied from the CPU cache to RAM. The same happens for the thread coming in, only it's data moves in the opposite direction.

Garbage collection will also take longer the more threads you have. When the garbage collector kicks in, it has to pause every thread and walk it's execution stack. The more threads you have, the more expensive this process is.

The optimum number of threads to have running on one machine is equal to the number of cores in the processor.

###Too many threads and the application doesn't scale
Asp.Net is a multi-threaded environment. Every new request is served on a different thread.

That is, every time a http request comes in, the thread pool checks if there is a thread available. If there isn't, it will have no choice but to create a new one.

For an app to perform optimally, it needs to server as many requests as it can, using as few threads as possible.

###Synchronous IO is bad

If your application performs IO synchronously, the executing thread becomes blocked. It just sits there wasting space, twiddling it's thumbs until the IO is complete.

What happens when another request comes into your application whilst this IO is happening?

A new thread - taking up memory, causing context switches, and slowing garbage collection down - is created.

What if that thread then blocks?

Another thread - using even more memory, causing even more context switches, and slowing garbage collection even further is created - so the cycle continues.

An Asp.Net application written in this manner will keel over with relatively few requests.

###Asynchronous IO is good
So how do we minimize the amount of new threads the thread pool has to create when our web application is busy?

By executing IO asynchronously.

By convention, methods that execute asynchronously end in the word **async**. By using these methods for IO, the executing thread is released and can be reused to serve another request. When then IO has finished, another thread (possibly the same one) will pick up the work where the call to the async method left off.

This totally maximises the server's resources and the application will handle exponentially more concurrent requests than before.

###Summary
If you want your web api application to scale, make sure you are writing asynchronous IO code. Pay attention when you're code:

- Reads or writes to a file
- Makes a database call
- Performs a web request

If the API you are using has a version of the method you need eding in **async** - USE IT!

