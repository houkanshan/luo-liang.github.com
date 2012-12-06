---
date: 2012-11-6 17:53:10
title: String Interning
layout: post
tags:
    - NET
categories:
    - Dev
---

在写.NET程序中，有时需要用有个标识符来区别对象（另一个方法是继承）的类别，此时总是有几种方案：

1）使用枚举

2）使用字符串

3）使用其他对象

 

为了清楚标示对象，一般使用1）和2）两种方式。

使用枚举的好处是，一切都清晰明了，但是枚举的弊端在于不可扩展——类别总是只有着一些，不能在运行时动态加入，这个问题和使用继承是一样的。

使用字符串的好处在于，总是可以动态地加入内容，如果不考虑错误的拼写的情况。但是，使用字符串一个潜在的问题在于，字符串可能需要占用额外的空间。我们知道，字符串在.NET中虽然是引用类型，但是它的表现却全部是值类型。例如

	var a = "a";
	
	var b = a;
	
	a = "b";

执行以上语句后，b仍然指向"a"，也就是说，每一次赋值都有一个新的字符串被创建。

当成千上万个对象使用字符串来标示时，这些用于标示类的域就有可能占用大量的内存。

这个问题是许多反对字符串作为标示符的人的主要论点。

 

 

 首先我们使用占用内存情况来判断这个论点的正确性。
	var StrArray = new string[1000000];
	for(int idx = 0;idx<StrArray.Length;idx++)
	{
		StrArray = "Unicode";
	}
	
	//Compiler Optimization off.
	//Memory usage 8,644K


在.NET中，String使用Unicode数组来表达。一个Unicode字符占用32个bit。在这里，我们生成一个具有一百万个Unicode的字符串。可以看到，整个程序（包含Visual Studio JIT Debugger）占用了8644K。

现在，我们把字符串长度加倍，再次查看程序运行内存。

	var StrArray = new string[1000000];
	for(int idx = 0;idx<StrArray.Length;idx++)
	{
		StrArray = "UnicodeUnicode";
	}
	
	//Compiler Optimization off.
	//Memory usage 8,808K

在这里，程序占用了8808K。

接着，再将字符串长增加。

var StrArray = new string[1000000];
	for(int idx = 0;idx<StrArray.Length;idx++)
	{
		StrArray = "UnicodeUnicode";
	}
	
	//Compiler Optimization off.
	//Memory usage 8,636K


程序占用了8636K。可以发现，与我们预期的不同，这一百万条相同的string并没有导致内存占用加倍。

 

在几乎所有新的语言中，字符串都是不可变的。这意味着你不可以像在C或C++中一样更改字符串。

例如:

	 var ImmutableString = "This is immutable";
	
	 ImmutableString[0] = ' '; //Compile error

字符串的不可变性带来了诸多好处，其中包括：

1）Yes,不用担心安全问题——它们总是不可变，这使得一些传统的攻击手段不可用。

	string Path  = "无害盘:\someFile";
	
	// Viruses will not be able to change path to "有害盘:\someFile";

	// Secure.
	
	File.Delete(Path);

2）Yes，不用担心线程安全问题——它们总是线程安全的

3）Yes，不会产生无数内存碎片：由于Char本身是值类型变量，当改变String中的字符时，新的Char会被建立，旧的char会在栈上存留。由于栈上的内容不属于垃圾回收范畴，若函数调用长时间不返回（如Main），那么这些旧的被替换的char永远不会被回收（！！）。

4）

在.NET CLR中，包含一个叫做String Interning的概念。String Interning技术允许String重用。.NET CLR从来不立刻为String申请内存，相反，当请求到来时，它总是使用哈希值来索引已经建立的字符串表，若哈希桶内包含这个值，那么CLR将直接返回指向该字符串的指针。CLR将不会向OS申请内存。这就解释了前边运行程序的怪异内存占用情况。



这样做的坏处是，对于不了解String Interning技术的开发员会造成混淆。在这段程序中，两个指针是指向同一个对象的。

在早于CLR 4之前的运行时中运行，你需要修改编译选项；在CLR 4中，CLR将不理会这个表一选项而直接开启String Interning技术。

 

因此，当碰到具有可扩展需求的应用时，总应该优先考虑String。

 

 

 