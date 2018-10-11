---
layout: "post"
title: "Building dynamic LINQ expressions"
date: "2018-10-11 10:02"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: linq c#
cover: 'assets/covers/'
description:
---

Developers as I saw when they are given a task of filtering the table dynamically they go for a `Stored Proceedure` and they write a dynamic query based on those if's and execute them. But, if you are using Entity Framework, then it might be a little difficult to frame that dynamic query in LINQ.

In this post, we will see how to build the dynamic LINQ queries. Building dynamic LINQ expressions is easy but you need to have an idea on [Func<T, TResult>](https://docs.microsoft.com/en-us/dotnet/api/system.func-2?view=netframework-4.7.2) and Expression Trees.

### Querying on what
For the purpose of the article, we will have a list of users and we will filter the list of users dynamically using the Func
delegate. So, we should be able to query any property on the user list data dynamically.

After that we will see how to query the data on a entity in entity framework using Expression Trees.

### The User class
{%highlight csharp%}
public class User
{
    public int ID { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
{%endhighlight%}

### What is a Func delegate?
> Encapsulates a method that has one parameter and returns a value of the type specified by the TResult parameter. - from msdn

Func<T> is a delegate type for a method that returns vlaue of the type T. You can use Func to reference a method.

If we want to multiply two numbers, then with Func delegate we can write that as

{%highlight csharp%}
//Func will take two integers as arguments to multiply
//and will return the output as integers
Func<int, int, int> multiply = (num1, num2) => num1 * num2;
Console.WriteLine(multiply(2, 3));
{%endhighlight%}

Back to our original dynamic linq, we'll use this Func delegate to have dynamic linq queries.

### Dynamic Query with func
If we want to get the specific id of the user in list of users then we will write the following LINQ on list of users.

{%highlight csharp%}
userData.Where(d => d.ID == val).ToList();
{%endhighlight%}
For FirstName?
{%highlight csharp%}
userData.Where(d => d.FirstName == val).ToList();
{%endhighlight%}
last name?
{%highlight csharp%}
userData.Where(d => d.LastName == val).ToList();
{%endhighlight%}

The user will also supply a property name to filter on. Can we have a switch statement to do this. (I know some of you don't like the switch).
{%highlight csharp%}
private static Func<User, bool> GetDynamicQueryWithFunc(string propName, object val)
{
    Func<User, bool> exp = (t) => true;
    switch (propName)
    {
        case "ID":
            exp = d => d.ID == Convert.ToInt32(val);
            break;
        case "FirstName":
            exp = f => f.FirstName == Convert.ToString(val);
            break;
        case "LastName":
            exp = l => l.LastName == Convert.ToString(val);
            break;
        default:
            break;
    }
    return exp;
}
{%endhighlight%}

In the above code snippet, we declared a Func delegate at the top and based on the property name we will assign the necessary linq query to the func delegate and return the func.

Once we get the dynamic LINQ query from the method, we will apply that on a list of user data.
{%highlight csharp%}
var dynamicExpression = GetDynamicQueryWithFunc(propertyName, value);
var output = userData.Where(dynamicExpression).ToList();
{%endhighlight%}
