---
layout: "post"
title: "Shortest word in a string with LINQ"
date: "2018-09-28 06:37"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: ["LINQ", "shortest word", "csharp"]
cover: 'assets/covers/short.jpg'
description: How to find the shortest word in a string using LINQ.
---
In this post, we'll try to solve a problem with LINQ.

### Problem
Given a string of words, return the length of the shortest word in the string.

Forget the edge cases of handling (empty or null).

> Ex: "The shortest string" => should output 3 for "The"

### Initial thoughts
So there will be a string which contains words. First, we'll split that string with empty space so that we'll get all the words in a string.

Now, we can sort the list of strings by length so that our shortest word will be in the first place and then we can perform a `FirstOrDefault` on it.

{% highlight csharp %}
public int ShortestWord(string s)
{
​    return s.Split(' ').OrderBy(x => x.Length).FirstOrDefault().Length;
}
{% endhighlight %}

or we can do an orderby and then do a select the length and return the first item.
{% highlight csharp %}
return s.Split(' ').OrderBy(i => i.Length).Select(i=>i.Length).First();
{% endhighlight %}

There can be many solutions like this.

Okay, we'll see what's the best or the clever solution

### Best Solution
We have the `.Min` extension method in LINQ. That should be sufficient to return the minimum length of the word.

{% highlight csharp %}
public int ShortestWord(string s)
{
​    return s.Split(' ').Min(x => x.Length);
}
{% endhighlight %}

That's it! That will return the minimum length of a word in a string.
