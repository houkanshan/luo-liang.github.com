---
date: 2012-10-20 21:55:03
title: 2 MSNET Runtime Type Helpers!
layout: post
tags:
    - MSNET
categories:
    - Dev
---

#Runtime helpers for .NET Types
Two runtime helpers for .NET developers that handle the problem of

* Determining if a given type is an unmanaged type.
* Determining if a given type is CLS Compliant.

##Unamaged type checker for fast-marshaling mechanism (see my next blog post)
We will need this type to do the checking when we are about to breach CLR Security verification mechanisms in my upcoming posts to provide fast marshal and unmarshal. This type is pretty easy to understand as it uses recursion to check if a type is an unmanaged type. By definition, an unmanaged type is a **structure** that:

* Contain only fields or properties of primitive types **Excluding System.String**
* Derived from System.ValueType
* Contains only **Unmanaged type** in any level of nesting.

The code is a part of my prototypical marshaler implementation.


    public static class UnmanagedTypeHelper
    {
       static Type[] unManagedPrimitives = new[] 
        { 
            typeof(bool),
            typeof(int), 
            typeof(uint), 
            typeof(long), 
            typeof(double),
            typeof(Int16), 
            typeof(Int64), 
            typeof(byte), 
            typeof(sbyte),
            typeof(UInt16),
            typeof(UInt64),
            typeof(IntPtr),
            typeof(UIntPtr),
            typeof(decimal)
        };
        public static bool IsUnmanagedType(Type type)
        {
            if (IsDerivedFromValueType(type) == false)
            {
                return false;
            }
            var members = type.GetMembers(System.Reflection.BindingFlags.NonPublic | System.Reflection.BindingFlags.Instance | System.Reflection.BindingFlags.Public);
            var fieldsAndProperties = members.Where<MemberInfo>(member =>
            {
                return (member.MemberType == MemberTypes.Field || member.MemberType == MemberTypes.Property) && member.GetCustomAttribute(typeof(System.Runtime.CompilerServices.CompilerGeneratedAttribute)) == null;
            });
            Type currentTestType;
            foreach (var memInfo in fieldsAndProperties)
            {
                if (memInfo.MemberType == MemberTypes.Field)
                {
                    currentTestType = ((FieldInfo)memInfo).FieldType;
                }
                else
                {
                    currentTestType = ((PropertyInfo)memInfo).PropertyType;
                }
                if (unManagedPrimitives.Contains(currentTestType) == false)
                {
                    if (IsUnmanagedType(currentTestType) == false)
                    {
                        return false;
                    }
                }
            }
            return true;
        }
        static bool isDerivedFromValueType(Type type)
        {
            while (type.BaseType != typeof(object))
            {
                if (type.BaseType == typeof(ValueType))
                {
                    return true;
                }
                type = type.BaseType;
            }
            return false;
        }
    }

We don't need to consider circular reference of structures. Since we're doing a runtime check not a compile time check, that kind of problem will be eliminated by the `csc.exe` itself.

##Exposing only CLS-Compliant-Named types in the assembly

The .NET Framework is never about a single language, it's about the whole ecosystem. The CLR is able to handle the casing problem like when two different properties can only be differentiated by their casing. Some script language, however, cannot.

In particular, the Microsoft?PowerShell? is one example of that kind of environment that forces the compliance of CLS.

An `InvalidOperationException` will be thrown by the runtime if any attempt is made to access a property that can only be distinguished with another property within a same class or structure definition by casing.

    class TestType
    {
        public int UppercaseMe;
        public int upperCaseMe;
    }

    PS C:\Users\WC> Add-Type -path D:\programs\tools\ExposeOnlyCLSCompliantTypeRule\bin\debug\ExposeOnlyCLSCompliantTypeRule.dll
    PS C:\Users\WC> $item = New-Object ExposeOnlyCLSCompliantTypeRule.TestType
    PS C:\Users\WC> $Item.uppercaseMe = 1
    -------------------------------------
    **InvalidOperationException - Failed to use non CLS Compliant type.**


It seems none of the Microsoft Code Analysis rules covers this, so next time you wish to export a library, make sure to check all public types are CLS Compliant. Those who are using scripts to load your library can really be released the burden of recompiling or reflecting to access these conflicting names.

This type chcker is also fairly straight forward:

    public static class CLSComplianceHelper
    {
        public static bool IsCLSCompliant(Type type)
        {
            var members = type.GetMembers(System.Reflection.BindingFlags.NonPublic | System.Reflection.BindingFlags.Instance | System.Reflection.BindingFlags.Public);
            Dictionary<string, bool> clsComplianceDictionary = new Dictionary<string, bool>();
            foreach (var memInfo in members)
            {
                if(clsComplianceDictionary.ContainsKey(memInfo.Name.ToLower()))
                {
                    return false;
                }
                else
                {
                    clsComplianceDictionary.Add(memInfo.Name, true);
                }
            }
            return true;
        }
    }

Use reflection to enumerate through all publicly exposed types using this type to enusure CLS Compliance fo the entire assembly.

I just got to know this when I was implementing my SmartIP system service, where I used a Native Windows WiFi API Wrapper class called `nativewifi.dll` by another developer. Since my process is done with Windows PowerShell, I have to load that dll to access features like 802.11 SSID. That dll is not originally CLS-Compliant and needs code refactoring to make it so.

In case you need to take a look [http://managedwifi.codeplex.com/](http://managedwifi.codeplex.com/ "Managed Wifi API")

And my work done with that API [http://rrurl.cn/65wfqm](http://rrurl.cn/65wfqm)
