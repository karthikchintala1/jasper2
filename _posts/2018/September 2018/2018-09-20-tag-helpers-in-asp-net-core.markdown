---
layout: "post"
title: "Tag Helpers in ASP.NET Core"
date: "2018-09-20 06:03"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: ["tag helpers", "ASP.NET Core", "razor"]
cover: 'assets/covers/taghelper.jpg'
description: Tag helpers in asp.net core. Tag Helpers allows the developer to use standard HTML tags in view while applying the server side code to customize the HTML elements in the razor files.
---

In this post, we'll see how to use the tag helpers inside razor files in ASP.NET Core application.

### Key Takeaways:
- What are Tag Helpers in ASP.NET core and why use Tag Helpers.
- Using tag Helpers.
- How to add/remove/opt-out of tag helpers.
- Tag helpers scope.

### What are Tag Helpers in ASP.NET Core?

Unlike normal [HTML Helpers in MVC](https://docs.microsoft.com/en-us/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs), the Tag Helpers in ASP.NET Core allow us to have server-side code to customize the HTML elements in razor files.

### Why use Tag Helpers?
- Enables HTML friendly development experience
- Provides intellisense for using server-side properties

### Using the Tag Helpers
We'll use the input element `asp-for` attribute and see how the tag helpers work. So, we've got a Post Model class here.

{% highlight csharp %}
public class PostModel
{
    [Display(Name = "Post ID")]
    public int PostID { get; set; }

    [Required]
    [Display(Name = "Post Name")]
    public string PostName { get; set; }
}
{% endhighlight%}

Now, go to the `Views/_ViewImports.cshtml` file and add the following

{% highlight csharp %}
@addTagHelper *, <your assembly name>
{% endhighlight%}

Now in our `Index.html` file, we'll just try to get the names of the PostModel into our Razor.

{% highlight html %}
@model CoreTagHelpers.Models.PostModel
<input asp-for="PostName" />
{% endhighlight %}

That should generate the following HTML with the `PostName` in the `id` and `name` attributes. And, it also generated `data-val`, `data-val-required` attributes as we have the `[Required]` data annotation decorated to the `Post name` property in our model class.

{% highlight html %}
<input type="text" data-val="true" data-val-required="The Post Name field is required." id="PostName" name="PostName" value="Hello">
{% endhighlight %}

The value for the input `Hello` is generated as I've created an object of PostModel in the controller action and passed it to the view.

{% highlight csharp %}
public IActionResult Index()
{
    var data = new PostModel { PostID = 1, PostName = "Hello" };
    return View(data);
}
{% endhighlight %}

### @addTagHelper
`@addTagHelper` directive makes the necessary tag helpers available to the view.

In the above input tag helper case, I've used a wildcard (*) to get all the model classes but you could use specific model classes only available to the view by giving a fully qualified name.

```
@addTagHelper CoreTagHelpers.Models.EmailTagHelper, CoreTagHelpers
```

### @removeTagHelper
This directive will remove the tag helper which was added. We can ignore a tag helper from `_ViewImports.cshtml` by adding @removeTagHelper in the view.

Adding @removeTagHelper in the `Views\_ViewImports.cshtml` will remove the tag helper from all the views.

### Opt out of tag helpers (!)

The opt-out character ("!") is used to disable the Tag Helper at the element level.

{% highlight html %}
<!label asp-for="PostID"></!label>
{% endhighlight%}

With the opt-out character, the HTML will not be generated for the label tag in the above case. We can use this opt-out character if we want to conditionally control rendering of the HTML elements.

### The scope of Tag helpers
We can add `_ViewImports.cshtml` to any view folder, and the view engine applies the directives from both the view folder and from the `Views\_ViewImports.cshtml`.

We can limit the Tag helper scope to specific view folder by adding a `_ViewImports.cshtml` inside the view folder. If we want to control it in a Home folder then adding `Views/Home/_ViewImports.cshtml` will make the view imports available within that folder itself.

### Default Tag helpers
There are many [tag helpers created by Microsoft](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.taghelpers?view=aspnetcore-2.0), which are included by default if you've created a dot net core project.

### Intellisense support for Tag helpers
Once you start writing any HTML element the icon for the HTML tag displays like this.

![Tag Helpers in ASP.NET Core: Tag helper symbol](assets\posts\tag_helpers\taghelpersymbol.png)

This indicates that the element is targeted by Tag Helpers. Normal HTML elements will just display the "<>" icon.

Once you add the `asp-for` attribute to the HTML element the color of the HTML element will be changed to purple.

![Tag Helpers in ASP.NET Core: Tag helper symbol](assets\posts\tag_helpers\labelaspfor2.png)

So, you can distinguish between a normal HTML tag and a tag helper tag.

### Conclusion
With the HTML helpers in ASP.NET MVC projects, it's hard for the front-end designer to HTML attributes to the elements in the file.

Tag helpers offer the server side code into the HTML elements while keeping the original HTML markup.
