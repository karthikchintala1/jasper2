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
tags: integers, list, linq
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

### A rough thought
Once you are given that problem, we tend to loop over the content and try to find out the type of each item and store that in another list and return that back.

{% highlight csharp %}
public static IEnumerable<int> GetOnlyInts(List<object> listOfObjects)
{
    var result = new List<int>();
    foreach(var item in listOfObjects)
    {
        if(item is int)
          result.Add((int)item);
    }
    return result;
}
{% endhighlight %}

But as we think through the list of objects is a List. So, there's no need of the for loop and additional result variable. We'll fine tune here using LINQ.

Ignore the method signature.

{% highlight csharp %}
return listOfItems.Select(a => {
        return new { success = a is int, val = a };
      })
      .Where(a => a.success) //filter only successful integers
      .Select(v => (int)v.val).ToList(); //return to a list of integers
});
{% endhighlight %}

This is a good solution and observe how we eliminated the failed cases in the where clause filter.

### Best solution
{% highlight csharp %}
public static IEnumerable<int> GetOnlyInts(List<object> listOfObjects)
{
    return listOfObjects.OfType<int>();
}
{% endhighlight %}

This is the best solution to return integers from the list of items. The 'OfType' extension method
returns only the specific type of item that is requested.

{% highlight csharp %}
return listOfItems.Select(a => {
        return new { success = a is int, val = a };
      })
      .Where(a => a.success) //filter only successful integers
      .Select(v => (int)v.val).ToList(); //return to a list of integers
});
{% endhighlight %}

### source code of OfType
https://referencesource.microsoft.com/#System.Core/System/Linq/Enumerable.cs,4ba4a3f8a5530e33

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

### Short solution:

{% highlight csharp %}
public static IEnumerable<int> GetOnlyInts(List<object> listOfObjects)
{
    return listOfObjects.OfType<int>();
}
{% endhighlight %}
