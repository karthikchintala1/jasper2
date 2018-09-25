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

{%highlight csharp%}
public abstract class TagHelper : Microsoft.AspNetCore.Razor.TagHelpers.ITagHelper { }
{%end highlight%}

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