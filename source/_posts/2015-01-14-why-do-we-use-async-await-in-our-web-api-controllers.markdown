---
layout: post
title: "Why do we use async await in our Web API controllers"
date: 2015-01-14 05:41:00 +0000
comments: true
categories: 
---

#DOCUMENT GOAL
To explain the advantage an application has when it's written asynchronously (asyncitis), and point out when no advantage is given.

#HEADING (must make the reader feel they’ll miss out by not reading)
Why do we use async await in our Web API controllers

#Body
#What to expect in this post
These days it seems as though everything you write has to be asynchronous. It's as if everyone has asynchitis.

But what is the reason for this obsession?

This post examines the advantages using async/await will givve you in your code, as well as pointing out when it's doing nothing for you.

#Threads are expensive
The bottom line is - threads are expensive. They use up a fair amount of memory (about 1Mb) and are slow to create (check this!).

Moving one thread out of a processor core and putting another one in is also a slow process, to be avoided if possible.

#Too many threads application doesn't scale
If, when your application is running, too many threads are created - it will not scale well.

Asp.Net is a multi-threaded environment. Every new request is served on a different thread. That is every time a http request comes in, a spare thread is used to serve that request. If there isn't a spare one available, the thread pool will create another.

The name of the game is to server as many requests as you can, using as few threads as possible.

#IO means threads block, thread pool makes more threads
IO is the biggest culprit when it comes to reasons that new threads have to be created. When your application fires a database request, web request, reads file contents, or anything else that gets handed off to hardware, the executing thread blocks waiting for the hardware operation to finish.

What happens when another request comes into your application whilst this IO is happening?

Well, if there isn't one available then a new thread - taking up precious resources - is created to serve the request. That in turn may then block during some IO whilst another request comes in, meaning another thread is created - and so the cycle continues.

#Dont use any resource when IO is happening 
Wouldn't it be great if we could use an idle thread that is waiting on IO to serve a new request? Well we can! And that is where Async/Await comes in.

When we use the await keyword, we are creating a seem in our code. We are essentially saying to the application "Here is a place where the executing thread may block. Release the thread at this point and pick it up at some point in the future.

#Not doing IO, no benefit

#Conclusion (Summarize and Monetize)