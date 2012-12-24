---
date: 2012-11-13 17:53:10
title: Tracking responsibilities with signed dlls
layout: post
tags:
    - Build
categories:
    - intelliEssay
---
The intelliEssay document format checker project is an endeavour of a team of 4. In the team, 2 are focusing on infrasturactural development and the rest on higher level development. Since the underlying `OpenXml` format is excessively complicated and the standard itself does not specify how the nodes should be presented?(rather, it is left to the host applications to determine how to render the nodes.I think this is much like the rendering engines in browsers, as different vendors have different engines). Sometimes, even the same vendors produce the different engines. In the case of `Microsoft Office 2003` and `Microsoft Office 2007,2010,2013`, the former use `Times new roman` as its default font for western languages, and in the later versions, `Calibri` is the default font.

This gives the infrastructure builders much burden, since not only the semantics of the nodes but the host application iteself should be considered when reporting formats to its users. With very limited documentations, infrastructure builders are very blind to their code limitationss and many times it is the users that find the possible flaws in the infrastructural codes. The failed calls will usually involve with a deep stack. Tracking failures and assigning resposiblities are thus difficult.

The purposed solution by `intelliSys` group is to require all `Dynamic Link Libraries` be signed by the developer. When the core of `intelliEssay` is up and running, it will inspect all its loaded dlls and reject any that is unsigned. `intelliEssay` core will first start itself, loads a library called `intelliEssay.ApplicationExceptionReportingRecordingRedistributing.dll` and all necesary dlls that are responsible for the checking. The `intelliEssay.ApplicationExceptionReportingRecordingRedistributing` is a wrapper collection that is used to keep track of runtime exceptions. The intelliEssay Core will swallow all exceptions found when checking, and dispatch the exception to the developer who did the code, then it rethrows the exception. `intelliEssay.ApplicationExceptionReportingRecordingRedistributing`, once notified of the problem, will contact intelliSys BugCheck system, which in turn will examine the details of the exception. From the exception source, we can learn where the exception is first thrown. The source indicates the dynamic link library that resulted in the exception. From the signing information, we will learn which developer is responsible for the problem.

For this, we can now dispatch exceptions to the developer.

