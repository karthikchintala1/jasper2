---
layout: "post"
title: "Get only specific types from List in C#"
date: "2018-09-10 17:49"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: filter, list, linq
cover: 'assets/covers/linq_oftype.jpg'
description: How to get only specific type of items from list in C#
---

This is a post to get the specific type of items from a mixed list of items. I have tried couple of solutions for the problem but it looks like there's more elegant solution than my solution.

### Problem
There will be a list of objects from which we have to extract only integers from it.

Ex:
{% highlight csharp%}
List<object> listOfObjects = new List<object>{ "tada", 10, "123", 22.0 "sttring", 1};
IEnumerable<int> result = GetOnlyInts(listOfObjects); //the result should b 10, 1
{% endhighlight %}

We've to implement the solution in `GetOnlyInts()` method so as to return only integers from it.

### A Draft thought
Once you are given that problem, we tend to loop over the content and try to find out the type of each item and store that in another list and return that back.

#### Solution 1

{% highlight csharp %}
public static IEnumerable<int> GetOnlyInts(List<object> listOfObjects)
{
    var result = new List<int>();
    foreach(var item in listOfObjects)
    {
        if (item is int)
          result.Add((int)item);
    }
    return result;
}
{% endhighlight %}

But as we think through, the `listOfObjects` is a List. So, there's no need of the for loop and add it to result variable. We'll fine tune here using LINQ.

Ignore the method signature.

#### Solution 2

{% highlight csharp %}
return listOfObjects.Select(a => {
        return new { success = a is int, val = a };
      })
      .Where(a => a.success) //filter only successful integers
      .Select(v => (int)v.val).ToList(); //return to a list of integers
});
{% endhighlight %}

This is a good solution and observe how we eliminated the failed cases in the where clause filter.

### Best/Short solution
{% highlight csharp %}
public static IEnumerable<int> GetOnlyInts(List<object> listOfObjects)
{
    return listOfObjects.OfType<int>();
}
{% endhighlight %}

The 'OfType' extension method only the specific type of item that is requested on.

Let's examine the source code of OfType extension method to see what it has.

### OfType() source code
[Source code link to OfType extension method](https://referencesource.microsoft.com/#System.Core/System/Linq/Enumerable.cs,4ba4a3f8a5530e33)

{% highlight csharp %}
public static IEnumerable<TResult> OfType<TResult>(this IEnumerable source) {
    if (source == null) throw Error.ArgumentNull("source");
    return OfTypeIterator<TResult>(source);
}

static IEnumerable<TResult> OfTypeIterator<TResult>(IEnumerable source) {
    foreach (object obj in source) {
        if (obj is TResult) yield return (TResult)obj;
    }
}
{% endhighlight %}

As you can see from the above source code of `OfType` method, it has the same old foreach statement on the `IEnumerable`. But it yield returns the values.

If the type of the object is `TResult`, then the value is returned by casting it to `TResult`.
