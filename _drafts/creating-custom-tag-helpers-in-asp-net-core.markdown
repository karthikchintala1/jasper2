---
layout: "post"
title: "Creating custom tag helpers in ASP.NET Core"
date: "2018-09-24 10:29"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: ["ASP.NET Core", "tag helpers", "custom"]
cover: 'assets/covers/'
description: How to create custom tag helpers in ASP.NET Core by implementing TagHelper class. In this post, we'll implement the process method of TagHelper class and use the setContent method of TagHelperOutput to set the content of the custom tag helper.
---
In this post, we'll see how to create a custom tag helper in ASP.NET Core project.

Custom tag helpers can be created by implementing TagHelper class. The Tag helper class has two methods that can be overridden.

### Tag Helper class:

{% highlight csharp %}
public abstract class TagHelper : Microsoft.AspNetCore.Razor.TagHelpers.ITagHelper { }
{% end highlight %}

### Methods of Tag helper class:

| Method name | Summary |
|------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Init(Tag Helper Context) | Initializes the ITagHelper with the given context. Additions to  Items should be done within this method to ensure they're added prior to executing the children. |
| Process(Tag Helper Context, Tag Helper Output) | Synchronously executes the TagHelper with the given context and output. |
| Process Async(Tag Helper Context, Tag Helper Output) | Asynchronously executes the TagHelper with the given context and output. |

For the purpose of this post, we'll override the `Process` method to synchronously execute the tag helper with the context and output our `HTML`.

### Use Case

In this article, we'll create a label with color by default. So, the user can supply the color by providing `color` as an attribute to the `colorlabel`.

Ex:

{% highlight html %}

<color-label color="red">red color</color-label>

{% end highlight %}

The above custom tag should output the label as red color.

### Implementing TagHelper class
We'll create a new `ColorLabelTagHelper` class and let's inherit from TagHelper class which is in `Microsoft.AspNetCore.Razor.TagHelpers` namespace.

Note that `TagHelper` at the end of the class is just a convention. We can omit the convention and just have the name directly.

{% highlight csharp %}

using Microsoft.AspNetCore.Razor.TagHelpers;

namespace CustomTagHelpers.TagHelpers
{
​    public class ColorLabelTagHelper : TagHelper
​    {
​        public string Color { get; set; }
​        public override void Process(TagHelperContext context, TagHelperOutput output)
​        {
​            output.TagName = "coloredlabel";
​            var colorStyle = $"color:{Color}";
​            output.Attributes.SetAttribute("style", colorStyle);
​        }
​    }
}

{% end highlight %}

The above code observe how we are setting the attributes for the tag using `output.Attributes.SetAttribute`. We can set all the html attributes that the element can support.

Once we've done that we need to use this tag helper in our views with `@addTagHelper` directive. Goto `_ViewImports.cshtml` file in the views folder and add the following line

{% highlight csharp %}

@addTagHelper *, CustomTagHelpers

{% end highlight%}

You could specify just the namespace of our custom tag helper but here I'm adding everything in the assembly.

### Using the custom tag helper in Views

We've implemented the custom tag helper that we can make use of.  Let's go a head and try to get our custom tag helper working.

For the purpose of this article, I'll clear all the HTML tags in the `Index.cshtml` file and add our awesome `colorlabel` custom tag.

Here is how the `Index.cshtml` looks after adding the custom tag.

{% highlight html %}
@{
​    ViewData["Title"] = "Index";
}

<h2>Index</h2>

<colorlabel color="red">the red color</colorlabel>
{% end highlight %}

Here is the output. Yay

![No color](D:\coderethinked\jasper2\assets\posts\custom tag helper\no_color.png)



Note that we didn't get the red color that we've expected. This is because we have the wrong syntax of custom tag helper.

The Pascal-cased class and the property names of the tag helpers are translated into their lower kebab case. So, inorder to use our custom helper class and property, we need to use `<color-label color="red">the red color</color-label>`. 

Let's try that in our Index.cshtml page and see it.

![Got the custom tag helper working](D:\coderethinked\jasper2\assets\posts\custom tag helper\with_red.png)

We got our custom tag helpers working now.

Let's take a look at the HTML generated

{% highlight html %}

<coloredlabel style="color:red">the red color</coloredlabel>

{% end highlight %}

We have the new tag in the HTML i.e., `coloredlabel` this is because we've specified the output tag name to be `coloredlabel`. And the style attribute on the tag generated as any normal HTML tag would generate. 

### Set Content



### Pre Content and Post Content

