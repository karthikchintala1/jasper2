---
layout: "post"
title: "Generating Sequence Numbers In LINQ Query"
date: "2018-07-19 18:15"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: C# LINQ ROW_NUMBER
cover: 'assets/covers/sequence.jpg'
description: generating sequence numbers in LINQ query
---
In this post, we'll see how to generate sequence numbers along with the data that we need in LINQ C#.

### Generating sequence numbers in SQL
In general, we can write a stored procedure something like this in `SQL` and then map this to a DTO or a ViewModel in C# so that we can get the sequence numbers along with the data.

{% highlight sql %}
SELECT     
          ROW_NUMBER() OVER (ORDER BY Column1) AS SeqNo,    
          DataColumn1, DataColumn2     
FROM  TestTable    
WHERE IsActive = 1
{% endhighlight %}
But, this isn't any good if we are using `Entity Framework`. Let's see what we can do to generate the sequence numbers in C#.

### Generating sequence numbers in C# LINQ
First, we'll write our query with DB context to fetch the data we want.

{% highlight csharp %}
var data = DbContext.TestTable  
                    .Where(a => a.IsActive)  
                    .Select(a => new {  
                        DataColumn1 = a.DataColumn1,  
                        DataColumn2 = a.DataColumn2  
                    });
{% endhighlight %}

The above LINQ query just pulls Datacolumn1, Datacolumn2 from TestTable if executed. Now, to get another column with sequence number, we'll just enumerate the results of the above LINQ query so that we'll get the columns we want.
Luckily, the `IEnumerable` interface provides an overload of `SELECT` which can do this.
Here is the `SELECT` overload definition.

{% highlight csharp %}
public static IEnumerable<TResult> Select<TSource, TResult>(  
    this IEnumerable<TSource> source,  
    Func<TSource, int, TResult> selector  
)
{% endhighlight %}
So, we'll see how it's done.

{% highlight csharp %}
var withSequence = data.AsEnumerable()  
                        .Select((a, index) => new {  
                            a.DataColumn1,  
                            a.DataColumn2,  
                            SequenceNo = index + 1  
                        });
{% endhighlight %}
As you can see in Line 01, we've enumerated the results from the LINQ to Entities and then we performed a `.Select()` on the enumerated set.
The index will have a starting value of zero so we'll increment it to make the indexing start from 1. It's up to you.
Here is the result.

![output](assets/posts/output.PNG)

### Why didn't we do it on the first LINQ query?


The first LINQ query is `IQueryable` and it doesn't have the overload of `.Select()` that has an index. So, we have to enumerate the results and then add sequence. So, in order to do it, we've to first make the `IQueryable` result to and `IEnumerable` result (as we did this in our second query using `.AsEnumerable()`).

Thanks for reading.
