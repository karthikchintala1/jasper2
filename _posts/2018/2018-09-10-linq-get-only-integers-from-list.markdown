---
layout: "post"
title: "LINQ Get only integers from List"
date: "2018-09-10 17:49"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags:
cover: 'assets/covers/'
description:
---

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
