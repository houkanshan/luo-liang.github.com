---
date: 2012-12-20 09:24:31
title: ʹ��intelliEssayƽ̨[2]
layout: post
tags:
    - intelliEssay
categories:
    - dev
---

��ප�Һ�.�ϴμ򵥽�����һ��intelliEssay�ĺ������.��������µ�intelliEssay���浮��,�ͽ���һ��������µĽ����Ͻ��п������������ļ����.

�������µ�intelliEssay Loader�ʺã�������CPU�㹻��,�Ϳ��Կ������ϸ΢������.

# Building your Checker #
������Ҫ��������Checker.����Checker�Ĺ��̺�ͼ�ν�����޹�ϵ.����intellIEssay Coreע��֮ǰ,��Ӧ�������ܵ�ȷ�����ļ��������ȷ��.

1.Visual Studio�а����½�һ��C#��������Ŀ,��������ΪintelliEssayCHECKERNAMEChecker,����CHECKERNAME��ռλ��.��������ѡ��ʹ��Ŀ��ΪX86,��������ʱΪ.NET 4.0,��������ѡ���Debugѡ��.

2.����,����Ҫ��Ӷ�intelliEssayCore��Ŀ������.�ڴ����ʱ��,����Ҫ���intelliEssayInterop��intelliSys.Agility��Ŀ.�����������෽��������ķ���.������ѡ������辶��������IntelliEssayInterop��intelliSys.Agility,������������Ҫ�����ʱ��ʵ���Ѿ�ʵ�ֵĹ���.

3.���������ռ�.��Ȼ��ʱ�Ⲣ��ʮ����Ҫ,����һ�����õ������ռ����������ּ�����Ĺ���.һ�����,intelliEssay.Checker.CHECKERNAMEChecker��һ�������ѡ��,Ҳ���������صĹ���.

4.д������ʵ����.����ʵ����Ӧ���̳�intelliEssay.Core.IEssayChecker�Ա㱻intelliEssay����ʱ����.��������������������

	class CHECKERNAMEChecker:intelliEssay.Core.IEssayChecker
	{
		
	}

 ����,�����Խ����ͣ����`IEssayChecker`��,Ȼ����`CTRL`��`.`��.��ʱ,��������Visual StudioΪ����ȫ�ӿڵķ���׮.

5.���`Run()`��`Cancel()`����.����֮��,��ҲӦ�����`GetDescription()`��`GetName()`����.��������������һ���ַ�������ָ���Ļ���Ϣ.������ѡ�������������,�������intelliEssay GUI�޷������ǽ��ж������л�.

6.��������Checker.�����Ƽ��ķ�����,�������ļ�����н��б��ز���.�������½�һ������Ϊ���������,���������ļ����.֮��,����Ҫ�½�һ��`Task`��.��������ڴ洢����һ���������������.����Ҫ��һ��`Task`ʵ���ṩһЩ������Ҫ�Ĳ���,����,`FilePath`,`TaskType`��`TaskSetting.StandardFilePath`.`FilePath`��Ҫ����ļ���·��,`TaskType`��ָ���������,Ŀǰֻ�ܽ���"nkub",��˼���Ͽ���ѧ����.���һ��ָ����׼��ʽ�ļ���·��.����Ҫʵ����һ�����ļ��������,Ȼ�������뵽`Task`��.��һϵ�в��迴����������:

	intelliEssay.Core.Task task=new Core.Task();
	task.FilePath = str;
	task.Initialize();
	CHECKERNAMEChecker myChecker= new CHECKERNAMEChecker();
	task.AddAChecker(myChecker);
	task.Run(RunMode.Console);

�����,`Run()`��������һ��ö�ٱ���.������intelliEssayӦ����ʲô��ʽ���ּ����.�����ʹ��������,���������������Ͽ��������intelliEssay�������.�����Խ����ļ��������Ϊ.dll����.exe�ļ�.

7.ע�����ļ����.��intelliEssayCore\debug\bin\�µ�registeredComponent.xml�ļ�.��<ArrayOfComponent></ArrayOfComponent>֮�����һ���µ�<Component>�ڵ�,���������ڵ�һ��.����ڵ��е�`Name`,`FilePath`��`Type`������������Ҫ��.��ɺ�,��������.��ҪС��`xsi:type="CheckerComponent"`��һ��.�����XML��������ν�����ЩԪ��.


	<?xml version="1.0"?>
	<ArrayOfComponent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
	  <Component xsi:type="CheckerComponent">
		<CheckerDescription>This is a default EssayChecker by intelliSys</CheckerDescription>
		<Type>Checker</Type>
		<Name>intelliEssayAlignmentChecker</Name>
		<FilePath>E:\Programs\ProjectEssayCompiler\TestDrive\intelliEssay\intelliEssayAlignmentChecker\bin\Debug\intelliEssayAlignmentChecker.exe</FilePath>
	  </Component>
	</ArrayOfComponent>

7.����IntelliEssay GUI Shell,���Ϳ����ڵ������п������ļ������!

����һ��,������������ξ����дһ�������.�Լ�intelliEssay�ṩ��Щ�����������ļ��.