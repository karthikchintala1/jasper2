---
layout: "post"
title: "How c# compiler looks at switch case statements"
date: "2018-07-19 20:49"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: C# IL Refactoring Switch
cover: 'assets/covers/binary_code.jpg'
description: c# compiler looking at switch case statements
---

Every developer writes a switch case statement at least once in their life of programming but as he/she understands the switch is no longer maintainable they tend to look for patterns and do refactoring.

In this post, we'll see what's actually inside of a switch case statement.
Here is the snippet of the code

```csharp
public void M(string x) {
        switch(x)
        {
            case "a":
                Console.WriteLine("a value");
                break;
            case "b":
                Console.WriteLine("b value");
                break;
            case "c":
                Console.WriteLine("c value");
                break;
        }
    }
```

and the IL for the above code

```csharp
.method public hidebysig
        instance void M (
            string x
        ) cil managed
    {
        // Method begins at RVA 0x2050
        // Code size 73 (0x49)
        .maxstack 2

        IL_0000: ldarg.1
        IL_0001: ldstr "a"
        IL_0006: call bool [mscorlib]System.String::op_Equality(string, string)
        IL_000b: brtrue.s IL_0028

        IL_000d: ldarg.1
        IL_000e: ldstr "b"
        IL_0013: call bool [mscorlib]System.String::op_Equality(string, string)
        IL_0018: brtrue.s IL_0033

        IL_001a: ldarg.1
        IL_001b: ldstr "c"
        IL_0020: call bool [mscorlib]System.String::op_Equality(string, string)
        IL_0025: brtrue.s IL_003e

        IL_0027: ret

        IL_0028: ldstr "a value"
        IL_002d: call void [mscorlib]System.Console::WriteLine(string)
        IL_0032: ret

        IL_0033: ldstr "b value"
        IL_0038: call void [mscorlib]System.Console::WriteLine(string)
        IL_003d: ret

        IL_003e: ldstr "c value"
        IL_0043: call void [mscorlib]System.Console::WriteLine(string)
        IL_0048: ret
    } // end of method C::M
```
###What's in the IL?

I'll walk through this IL, at Line 12 in the IL there's a string equality checking which checks for the case statement to match once it is matched at Line 18 a `brtrue.s IL_0028`(a branch to target if the value is non-zero) is issued, this will check the result of the above line (the equality comparison).

Let's consider our best case to match "a" so the control is now transferred to IL_0028 this is where our write line statement at. So, we'll print that out.

### What the IL looks like?

So, to give a glimpse of that IL in C# it is like this

```csharp
public void M(string x)
{
    if (!(x == "a"))
    {
        if (!(x == "b"))
        {
            if (x == "c")
            {
                Console.WriteLine("c value");
            }
        }
        else
        {
            Console.WriteLine("b value");
        }
    }
    else
    {
        Console.WriteLine("a value");
    }
}
```
You might be refactoring the above if statements for a cleaner switch case statement but you'd end up with the above code when the compiler transforms the switch.

### Wrapping up
If you ever refactor an if-else chain to a simple switch case statement then it is not so good but will be clean as compiler generates the if-else chain for you instead you ending up in tangled between if-else chain.

One tip for those who refactor these if-else statements to switch is that run code metrics tools to see if there is any improvement on the maintainability index. I'm sure there won't be if the code metric tool really sees this IL stuff.

But, the switch is also not a good thing to do if you think from the design perspective of the application. That is why people tend to introduce the polymorphism instead of switch design or use dictionary patterns.

Thanks for reading.
