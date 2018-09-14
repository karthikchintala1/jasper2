---
layout: "post"
title: "Misleading LOC in Code metrics?"
date: "2018-09-14 11:10"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: c# visualstudio codemetrics
cover: 'assets/covers/'
description: Showing more Lines of Code in Code Metrics Results?
---

This post is about Code Metrics results in Visual studio is showing more lines code than what you've wrote in a C# file.

Recently, I've switched from Visual Studio 2015 to Visual Studio 2017. VS 2015 has the [health indicator extension](https://marketplace.visualstudio.com/items?itemName=Jean-MarcPrieur.MicrosoftCodeLensCodeHealthIndicator-15077), which will show us the maintainability index of the method. But, the health indicator extension is not available for Visual Studio 2017. So, I have to run the [Code Metrics](https://docs.microsoft.com/en-us/visualstudio/code-quality/code-metrics-values?view=vs-2017) results in VS 2017.

I wrote the following LINQ query and it showed 9 Lines Of Code as I saw in Code metric results. But, as you can see I only have one line of code in LINQ.

{% highlight csharp%}
private void GetValues(long id)
{
    DataContext.Student.Where(e => e.Id == id
                                 && e.IsActive).FirstOrDefault();
}
{% endhighlight %}
