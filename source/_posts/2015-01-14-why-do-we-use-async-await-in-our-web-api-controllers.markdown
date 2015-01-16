---
layout: post
title: "Why do we use async await in our Web API controllers?"
date: 2015-01-14 05:41:00 +0000
comments: true
categories: 
---
These days it seems as though everything you write has to be asynchronous. It's as if everyone has asynchitis.

But what is the reason for this obsession?

This post examines the advantages using async/await in your controllers. I'll also point out when I think it's a bad idea.

###Threads are expensive
The bottom line is - threads are expensive. They use up a fair amount of memory and slow performance down.

The optimum number of threads to have running on one machine is equal to the number of cores in the processor.

Context switching (moving one thread out of a core and putting another one in) is a slow process.

Also garbage collection takes longer the more threads you have. This is because the GC has to pause every thread and walk it's execution stack to find objects for collection.

###Too many threads and the application doesn't scale
It follows then that if too many threads are created - your applications' performance will suffer.

Asp.Net is a multi-threaded environment. Every new request is served on a different thread. That is every time a http request comes in, a spare thread is used to serve that request. If there isn't a spare one available, the thread pool will create another.

The name of the game is to server as many requests as you can, using as few threads as possible.

###Synchronous IO means threads block

When your application fires a database request, web request or reads file contents synchronously, the executing thread blocks. It sits there twiddling it's thumbs getting fat on your server's resources waiting for the IO to comoplete.

What happens when another request comes into your application whilst this IO is happening?

A new thread is created!

A new thread taking up precious resources - is created to serve the request. That in turn may then block during some IO whilst another request comes in, meaning another thread is created - and so the cycle continues.

Such an application will keel over when faced with too many requests.

###Writing non-blocking code
Wouldn't it be great if we could use an idle thread that is waiting on IO to serve a new request? Well we can! And that is where async/await comes in.

When we use the await keyword, we are creating a seem in our code. We are essentially saying to the application "Here is a place where the executing thread may block. Release the thread at this point and pick it up at some point in the future.

###No IO, no benefit
If you're code isn't performing any IO, then there's probably no point writing your controllers with async/await. I can't see any benefit of doing so anyway. I think the code will end up looking more complicated than it need be. Feel free to comment if you disagree.

###Summary
It is of great importance that you write asynchronous code if you want your web api application to scale. Writing code that blocks will mean the server your app runs on can handle far fewer requests than if you wrote the code asynchronously.

