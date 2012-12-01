---
date: 2012-10-30 14:53:20
title: People love Smart highlighter
layout: post
tags:
    - SILVERLIGHT
categories:
    - Dev
---
#Syntactic Highlighter and intelliSense#
## intelliSense and syntactic highlighting ##
One of the greatest invention in the century in programming IDEs is, I think, the birth of intelliSense and syntactic highlighting. These aid greatly in improving the experiences of coding by 

- letting you see the outline of document more easily, highlighted words are much like the "first of all" and "second" in our compositions.
- saving time when typing long long names, especially when the name is mixed with capitalized and noncapitalized letters. (But one potential drawback here is that developers (like me) are tend to be less careful in giving their variable names when working on such intelligent IDEs, and they tend to make their variables' names more like 'description' rather than 'summary', because they know, no matter how long their variable names are, intelliSense will make typing them instant. You can run Microsoft Variable-Name Rules to see how many problems are found in your document in Visual Studio to testify this (at least it was so true to me)).


As more and more blog posts on programming skills appeared over the Internet, the need for the above features grow, because no one feel likes reading uncolored codes.

Numerous solutions are developed by front-end designers, but many of them are unsatisfactory (this is definitely not to blame front-end designers, as 1) it is sometimes just impossible to do the coloring 2) there are simply too many programming languages to cover and each may have its unique grammar). Some of them only cares about *built in* keywords, others care only syntactic meaning and ignores semantic meaning and thus colors 'keyword' as 'keyword'. For example in C#, all `public, class, select` are keywords (Bold words (colored words) are surrounded by **):

    **public** **class** Select
    {
        //this is an empty **class**
    }

This is unfortunate, since the second **class** shouldn't be colored in the keyword way as it appears in comments and should be treated as a part of comment. Others, although do provide some way of semantic processing, do not pick up newly defined symbols along the way, or picks everything along the way (Sublime Text 2). But most serious problem occurs when the document being displayed is Partial, so highlighter has no way to understand the type of everything even semantic processing is incorporated.

##What about intelliSense##
Having talked about highlighting, it's time to shift to intelliSense, and soon you will see, good intelliSense and highlighter go hand in hand with each other.

There are generally 3 broad categories of intelliSense implementation I have seen so far: semantic based (Visual Studio), syntactic based (Sublime Text), or syntactic based with pattern recognitions (Visual Assist). 

To illustrate the difference with the three kinds of intelliSense, we can use the following test pattern in C#:

    namespace TestHighlighter
    {
        static class Program
        {
            public static class Test
            {
                public void TestMethod()
                {
                    Test test = new TestHighlighter.
                }
            }
        }
    }

There is a no syntactic problem but semantic problem with the statement. By rules, `static` means `abstract` and `sealed` in some ways. So when typing the `new` keyword, correct intelliSense should prompt nothing as the statement is wrong. In Visual Studio, the intelliSense is blind to the `new TestHighlighter.` statement as nothing to do with `Test` prompts out, because it knows initializing a static class has no meaning. In Sublime Text 2, everything pops out, because Sublime Text 2 knows a token needs to be appended to the dot operator, but has no idea what should be appended. In Visual Assist, the `Test` comes out because VA knows `Test` is added to the program and the `Test test` statement tells it to initialize a `Test` instance, but it has no idea what the `static` keyword means.

Generally, although works very well, semantic based intelliSense needs compiler support, and this is why this kind of intelliSense do bad in dynamic languages like JavaScript or Windows PowerShell - they don't even have a compiler but only a interpreter, and they are type-unsafe so type-inference can never be done. Besides, who would bother installing so many compilers for each language displayed on the website for the pure sake of highlighting texts? Highlighter goes with intelliSense, when compiler recognizes a token, it judges its type, and appends its metadata in the list, and then predicts next acceptable token. This series of action will give UI enough information on what color to use to display them and what else is possible to prompt for user to choose.

One anomaly of this is the C++/CLI, whose intelliSense has been cut due to various reasons. See [http://blogs.msdn.com/b/vcblog/archive/2011/03/03/10136696.aspx](http://blogs.msdn.com/b/vcblog/archive/2011/03/03/10136696.aspx "Here").

## Go back to where we are ##
Having understood different problems and facts about intelliSense and code highlighting, it's time for us to give it a solution, to at least mitigate the problem of implementational complexity and code readability.

#### A Silverlight based implementation ####
Reasons to choose Silverlight are:

- Browser unspecific and platform independent
- Easy to implement
- All PC, Mobile and Web

I have used very simple finite machine implementations to deal with C# code semantics, and have avoided most problems above. This implementation also allows user to add known tokens. (For example, user can tell the program that all type in `System` should be added, therefore, common types, like `System.DateTime`, will be colored). Also this parser remembers the token it has processed, and will automatically assign them proper types. So types you defined in the document will be understood by the parser and highlighter without problem.

![Preview](http://b.hiphotos.baidu.com/album/s%3D1400%3Bq%3D90/sign=9aa516860fb30f24319ae807f8a5ea32/b17eca8065380cd7700f1f0aa144ad34588281c0.jpg)

As before, you can always get a copy of code from my SkyDrive. But the lack of a proper interface is preventing me from porting it to web...

Wish I will come up with one later.


