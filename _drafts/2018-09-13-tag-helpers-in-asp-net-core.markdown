---
layout: "post"
title: "Tag Helpers in ASP.NET Core"
date: "2018-09-13 07:03"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: ["tag helpers", "ASP.NET Core", "razor"]
cover: 'assets/covers/'
description: Tag helpers in asp.net core
---
In this post, we'll see how to use the tag helpers inside razor files in ASP.NET Core application.

### Key Takeaways:
- What are Tag Helpers in ASP.NET core and why tag helpers.
- How to use tag Helpers.
- how to add/remove/opt out of tag helpers.
- Tag helpers scope.

### What are Tag Helpers in ASP.NET Core?

Unlike normal [HTML Helpers in MVC](https://docs.microsoft.com/en-us/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs), the Tag Helpers in ASP.NET Core allow us to have server side code to customize the HTML elements in razor files.

### Why use Tag Helpers?
- Enables HTML friendly development experience
- Provides intellisense for using server side properties

### Using the Tag Helpers
We'll use the label tags `for` attribute and see how the tag helpers work. So, we've a Post Model class here.

{% highlight csharp %}
public class PostModel
{
  public int PostID {get; set;}
  public string Name {get; set;}
}
{% endhighlight%}

Now in our `Index.html` file, we'll just try to get the names of the PostModel into our Razor.

{% highlight html%}
@model PostModel
<label asp-for="Name"></label>
{% endhighlight %}

And go to the `Views/_ViewImports.cshtml` file and add the following

```
@addTagHelper *, <your assembly name>
```

That should generate the following HTML with the `Name` in the label tag

{% highlight html%}
<label for="Name">Name</label>
{% endhighlight %}

If we've the data annotated properties on the Model class that will also be generated with the `asp-for` tags.


### @addTagHelper
@addTagHelper directive makes the necessary tag helpers available to the view.

In the above case, I've used wildcard (*) to get all the model classes but you could use specific model classes only available to the view by giving fully qualified name.

```
@addTagHelper TagHelpersCore.Models.PostModel
```

### @removeTagHelper
This directive will remove the tag helper which was added. We can ignore a tag helper from `_ViewImports.cshtml` by adding @removeTagHelper in the view.

Adding @removeTagHelper in the `_ViewImports.cshtml` will remove the tag helper from all the views.

### Opt out of tag helpers (!)
### scope of Tag helpers
We can add `_ViewImports.cshtml` to any view folder, and the view engine applies the directives from both the view folder and from the `Views\_ViewImports.cshtml`.

We can limit the Tag helper scope to specific view folder by adding a `_ViewImports.cshtml` inside the view folder. If we want to control it in a Home folder then adding `Views/Home/_ViewImports.cshtml` will make the view imports available within that folder itself.

[TODOS]
- Tag helpers scope brief
- remove tag helper
