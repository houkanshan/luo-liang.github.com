---
date: 2012-12-01 17:53:10
title: Timers with Tomb stoning
layout: post
tags:
    - SILVERLIGHT
categories:
    - Dev
---

Many years ago, when Apple introduced its first iPhone, they have embraced the idea of isolation, where applications have no other way to interact with each other rather than system calls. Many iPhone 1 users are complaining the lack of background tasks and this forced the incorporation of Push Notifications and other technologies.

Windows Phone, a good learner, has also decided to say goodbye to the old architecture of Windows Mobile. The isolation technology primarily used in mainstream version of Windows Phone is called Tomb stoning, where process states, registers, call stacks and other process-related data are stored in memory. When the user deactivates the process, the processor stores all data mentioned above and perform a context switch. When the user goes back to the process again, the processor will pick up all data left in the memory and continue to run just as if it was running all the time. When memory is low, the OS kicks out some of the applications' tombstones from memory (that's one of the reasons why Microsoft recommends saving user data as early as possible rather than leaving it until the application exits).

Note that tomb stoning isn't limited only to Windows Phone 7 and 8s since it also relates to the new Metro user interface applications in Windows 8 PCs and Windows RT tablets. In Windows 8 and RT, as well as in Windows Phone, a modern Internet Explorer is introduced, so tomb stoning not only applies to user applications, but also to webpages that are hosted in Internet Explorer.

This looks subtle but if we think further, some interesting problems will occur. Let's first see an example of such problems.

    visit http://www.baidu.com using a modern Internet Explorer
    type in 北京时间 (Beijing Time in Chinese)
    click 百度一下
    Observe that the first result will be a running clock showing the GMT+8 time
    Press Start Button to navigate away from modern Internet Explorer and record the displayed time in the webpage
    Wait a minute (literally)
    Navigate back to modern IE.
    Notice the time remains 1 minute earlier.

Please be noted we are not talking about how the time control is implemented: either by using a ticker that triggers every second, or by using a dedicated thread that sleeps at a one second interval in a loop. We also do not care about whether the above implementations are correct (forget about all `APC Queues` and other that are internal to Windows mechanisms that may invalid the ticks).

The problem is caused by tomb stoning. Although the Internet Explorer has knowledge of its being de- and re-activated, it has no way to inform any of its webpages to perform a proper action: it should not fire a reload or refresh, because the user might be doing a PayPal transaction and refreshing might cause a duplication.

In this case, the problem might not be severe because the user, upon wondering why the time isn't going forward, will manually perform a refresh and correct the time. However, the discrepancies of user experiences across browsers will confuse the user at least once if not always.

However, imagine you're a vendor of an offline game, say, a Wordament offline version that allows users to play without connection to the Wordament server (the game settings and game boards are updated periodically when the user is connected to the Internet). The user might trick the timer by deactivating the application after remembering the game board. So when they go back, they will be earning high scores easily and thus rank high. This will cause a severe problem, since those who playing hard will definitely lose to those who cheat and thus makes the game lame.

In both the online and offline situations, different approaches can be made to eliminate the problem caused by tomb stoning. When online, a proper heart beat can be maintained across server and client. Thus the timer in the client is periodically updated. In offline situations, when the host application is deactivated or reactivated, a `DateTime.Now` will be recorded and the interval of the 2 datetimes is what should be added/subtracted to/from the running stopwatch/timer.
