---
date: 2012-12-10 18:23:07
title: 使用intelliEssay平台[1]
layout: post
tags:
    - intelliEssay
categories:
    - Dev
---

哈喽大家好。经过一年沉静，intelliEssay终于要被再次构建了。之前我们把intelliEssay称为一个程序，现在，我们不妨把它称为平台。

按照开发计划，我们把任务鲜明地分成了基础设施（平台建设）和检查器（平台上的应用）两个部分。就像操作系统一样，检查器（应用）并不直接操作“内核数据”（OpenXml），而是通过“陷阱”（基础设施API）完成工作。

让我们一起看看intelliEssay是如何工作的。
# 构件 #
首先，我们看一看intelliEssay是如何在各个构件间传递控制流的。在开始之前，需要先了解intelliEssay平台的构成。

- intelliEssay的核心部分，我们称之为intelliEssayCore.dll，它定义了intelliEssay.Core.*, intelliEssay.Environment命名空间的绝大部分，是控制、协调整个intelliEssay的内核。它的工作主要有：定义主要的类、线程同步、向外公开功能性接口、执行文档分析。在检查中必不可少的Task类就是在其中定义的。 intelliEssayCore还负责平台功能侦探功能（intelliEssay Platform Feature Probing)，它用于发现、管理、加载intelliEssay的扩展项。

- intelliEssay的开发助手部分，我们称之为intelliEssayInterop.dll，它定义了intelliEssay.Interop.*的绝大部分，是一个与intelliEssayCore松耦合的类库。其中包含获取文档结构、格式的接口。在检查中必不可少的RunInfoEx（获取字体信息）和ParagraphInFoEx(获取段落信息）就是在这里定义的。除此之外，intelliEssayInterop.dll还有自己的缓存逻辑用于快速存取反复访问的文档结构信息。intelliEssayInterop.dll依赖于intelliEssayCore中的类型，因此在引用它之前需要引用intelliEssayCore.dll

- intelliEssay的检查器部分，我们称之为intelliEssay*Checker.dll，它们共同定义了intelliEssay.Checkers.*命名空间。这就是检查器需要编写的内容。一个检查器是继承了intelliEssay.Core.IEssayChecker接口的实类型。intelliEssay的检查器在运行时会被动态装入，但是要被intelliEssay.Core发现，必须先向intellIEssay平台注册（目前这一步可以省略为了便于调试）。

- intelliEssay的图形部分，我们称之为intelliEssayGraphicsShell.exe,它定义了intelliEssay.Graphics.Shell, intelliEssay.Graphics.Globalization的绝大部分。前者定义了一个适合intellIEssay的图形界面，而后者用于处理多语言的消息。

- intelliEssay语言包，我们称之为intelliEssay.Graphics.Shell.*LANGUAGENAME*.dll，这些语言包会被放在root\langPacks下，载intelliEssay图形界面加载的过程中，会动态加载这些语言包。

- intelliSys OpenXml敏捷包，我们称之为IntelliSys.Agility.Openxml.dll，这个类库包含了存取OpenXml有关类型的统一接口。例如，可以快速存取一个诸如Space的Value内容。而使用正常的流程，要访问Space.Val.Value，需要首先对Space检查Null，再对Val检查Null，然后再存取Value。而是用bool ValHelper.TryGetValue<T>(object o, out T value)泛型，则可以对任何类型的o进行此类工作，若读取成功，结果就出现在Value中。

- 其他废弃的内容。在intelliEssay开发转向CodePlex平台之前，intelliEssay用自己的工具协助金山快盘同步代码。intelliEssayHelpAgent用于负责生成intelliEssay代码帮助文档、团队通知、同步代码、链接库注册。intelliEssay.ApplicationExceptionReportingRecordingRedistributing.*命名空间用于跟踪intelliEssay运行时错误，根据链接库注册的签名通知intelliEssayBugCheck程序指派给特定的程序员并进行邮件通知。

