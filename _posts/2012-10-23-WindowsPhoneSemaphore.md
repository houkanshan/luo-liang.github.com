---
date: 2012-10-23 14:53:20
title: A semaphore for Windows Phone
layout: post
tags:
    - MSNET
categories:
    - Dev
---

#Semaphore for Windows Phone
Everyone will be happy with Microsoft .NET Compact Framework until they take a look at the System.Threading namespace. Our old friends, like Semaphore and other fancy locks are all gone.

 Why is that? Perhaps that's because the runtime is the same with Silverlight and the fact Windos Phone 7s can only support one processor thus won't be heavyily threaded. So, Microsoft thought "We're gonna cut the .NET Framework to its minimum!".

Specifications showed the situation might be different, as Windows Phone 8s are supporting multiple processors, will our locks be back? I don't think so, as the Silverlight 5 remains unchanged in regard of this part.

But the good news is, Microsoft hasn't removed other locks we can use to make our own locks, like, the `AutoResetEvent` and `Monitor`. These leave the possibility of building our own Semaphore implementations.

One of the basic lock we so need is the `Semaphore`, because although only equipped with limited horsepower, our Windows Phones do support threading. In this case many IO operations that take long time can take advantage of this. For example, we're asking a series of webpages that have the same format of "http://somesite/pages/[index]", and we're pulling 150 pages (so index varies from 1 to 150). If you pull pages serially, the user would wait long enough to drain their paitence and kill the application, but if we pull the pages all together in 150 wild calls to `ThreadPool.QueueUserWorkItem` or `Begin/EndInvoke` pairs, we're not going to yield good result as either the CPU would spend all its time contextual switching or the network bandwidth isn't high enough to support 150 simutaneous GETs. 

Just FYI, In the full version of .NET Framework Semaphores come in many flavors, like the `Semaphore` and `SemaphoreSlim`.

I built an application that takes advantages of my own Semaphore implementation for my University, which is used to check idle classroom now. This application was built in part to satisfy my need as well as other Windows Phone Nankaiers' needs to find an idle room to study and the desire to prove Windows Phone was **as capable as Andriods and iPhones** in response to an argument that asserted "This kind of application can never be found on WP Marketplace". 

Speaking of this application, it is my desire to show it off to anyone with a Windows Phone, and the place with the highest intensity of Windows Phone is the Microsoft building. I asked one girl sitting next to me to test drive it and she gave me a score of 4/5, with the comment "useful any(but) expect more improvements" and the only DSAT that resulted a non-5 was that the course list couldn't be refreshed in real-time (meaning no push notification for a sudden change of a class). Well, I agreed.

In case you're a Nankaier and a Windows Phone user, take a look at my app and be sure to tell me about your ideas:
[http://www.windowsphone.com/zh-cn/store/app/%E5%8D%97%E5%BC%80%E5%A4%A7%E5%AD%A6%E6%95%99%E5%AE%A4%E4%BF%A1%E6%81%AF%E6%9F%A5%E8%AF%A2/41e5547d-066c-43e4-b8ce-283cbd3eb4e1](http://www.windowsphone.com/zh-cn/store/app/%E5%8D%97%E5%BC%80%E5%A4%A7%E5%AD%A6%E6%95%99%E5%AE%A4%E4%BF%A1%E6%81%AF%E6%9F%A5%E8%AF%A2/41e5547d-066c-43e4-b8ce-283cbd3eb4e1 "南开大学教室信息查询").

Back to the Semaphore, if you need the semaphore, pay a visit and download it from here. Any bugs you can tell me.
[http://sdrv.ms/OYi36j](http://sdrv.ms/OYi36j)

