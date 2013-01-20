---
date: 2012-12-20 18:23:07
title: 使用intelliEssay平台2
layout: post
tags:
    - intelliEssay
categories:
    - Dev
---



哈喽大家好.上次简单介绍了一下intelliEssay的核心组件.这次由于新的intelliEssay界面诞生,就介绍一下如何在新的界面上进行开发、调试您的检查器.

首先向新的intelliEssay Loader问好！如果你的CPU足够慢,就可以看到许多细微动画噢.

# Building your Checker #
首先您要建立您的Checker.建立Checker的过程和图形界面毫无关系.在向intellIEssay Core注册之前,您应当尽可能低确保您的检查器的正确性.

1.Visual Studio中按下新建一个C#命令行项目,将它命名为intelliEssayCHECKERNAMEChecker,其中CHECKERNAME是占位符.调整编译选项使其目标为X86,调整运行时为.NET 4.0,调整编译选项开启Debug选项.

2.接着,您需要添加对intelliEssayCore项目的引用.在大多数时候,您需要添加intelliEssayInterop和intelliSys.Agility项目.那里包含了许多方便您代码的方法.您可以选择另辟蹊径而不引用IntelliEssayInterop和intelliSys.Agility,但是您可能需要花许多时间实现已经实现的功能.

3.更新命名空间.虽然有时这并不十分重要,但是一个良好的命名空间有助于区分检查器的归属.一般而言,intelliEssay.Checker.CHECKERNAMEChecker是一个不错的选择,也是我们遵守的规则.

4.写下您的实体类.您的实体类应当继承intelliEssay.Core.IEssayChecker以便被intelliEssay运行时载入.它看起来可能像这样：

	class CHECKERNAMEChecker:intelliEssay.Core.IEssayChecker
	{
		
	}

 现在,您可以将光标停留在`IEssayChecker`上,然后按下`CTRL`和`.`号.这时,您可以让Visual Studio为您补全接口的方法桩.

5.完成`Run()`和`Cancel()`方法.除此之外,您也应当完成`GetDescription()`和`GetName()`方法.后两个方法接受一个字符串用以指明文化信息.您可以选择无视这个参数,但后果是intelliEssay GUI无法对它们进行多语言切换.

6.调试您的Checker.我们推荐的方法是,您在您的检查器中进行本地测试.您可以新建一个类作为函数的入口,并测试您的检查器.之后,您需要新建一个`Task`类.这个类用于存储关于一次任务的所有内容.您需要向一个`Task`实例提供一些至关重要的参数,例如,`FilePath`,`TaskType`和`TaskSetting.StandardFilePath`.`FilePath`是要检查文件的路径,`TaskType`是指任务的类型,目前只能接受"nkub",意思是南开大学本科.最后一项指定标准格式文件的路径.您需要实例化一个您的检查器类型,然后将它加入到`Task`中.这一系列步骤看起来像这样:

	intelliEssay.Core.Task task=new Core.Task();
	task.FilePath = str;
	task.Initialize();
	CHECKERNAMEChecker myChecker= new CHECKERNAMEChecker();
	task.AddAChecker(myChecker);
	task.Run(RunMode.Console);

额外地,`Run()`方法接收一个枚举变量.它告诉intelliEssay应当以什么方式呈现检查结果.如果您使用命令行,您可以在命令行上看到整齐的intelliEssay错误输出.您可以将您的检查器编译为.dll或者.exe文件.

7.注册您的检查器.打开intelliEssayCore\debug\bin\下的registeredComponent.xml文件.在\<ArrayOfComponent\>\</ArrayOfComponent\>之间加入一个新的\<Component\>节点,就像其他节点一样.这个节点中的`Name`,`FilePath`和`Type`属性是至关重要的.完成后,就像这样.您要小心`xsi:type="CheckerComponent"`这一句.这告诉XML解析器如何解析这些元素.


	\<?xml version="1.0"?\>
	\<ArrayOfComponent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema"\>
	  \<Component xsi:type="CheckerComponent"\>
		\<CheckerDescription\>This is a default EssayChecker by intelliSys\\</CheckerDescription\>
		\<Type\>Checker\</Type\>
		\<Name\>intelliEssayAlignmentChecker\</Name\>
		\<FilePath\>E:\Programs\ProjectEssayCompiler\TestDrive\intelliEssay\intelliEssayAlignmentChecker\bin\Debug\intelliEssayAlignmentChecker.exe\</FilePath\>
	 \</Component\>
	\</ArrayOfComponent\>

7.运行IntelliEssay GUI Shell,您就可以在第三步中看到您的检查器了!

在下一节,将向您介绍如何具体编写一个检查器.以及intelliEssay提供哪些方法便利您的检查.