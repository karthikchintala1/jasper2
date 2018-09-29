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
tags: ["ASP.NET Core", "custom tag helpers", "PreElement", "PreContent", "PostContent", "PostElement","SetContent"]
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

{% endhighlight %}

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

{% endhighlight %}

The `Color` property in the above code acts as an attribute to the `coloredlabel`. So, you can set color from the HTML.

Observe how we are setting the attributes for the tag using `output.Attributes.SetAttribute`. We can set all the html attributes that the element can support.

Once we've done that we need to use this tag helper in our views with `@addTagHelper` directive. Goto `_ViewImports.cshtml` file in the views folder and add the following line

{% highlight csharp %}

@addTagHelper *, CustomTagHelpers

{% endhighlight%}

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

![failed markup in custom tag helper](D:\coderethinked\jasper2\assets\posts\customtaghelper\no_color.png)

Note that we didn't get the red color that we've expected. This is because we have the wrong syntax of custom tag helper in place.

The Pascal-cased class and the property names of the tag helpers will be translated into their lower kebab case. So, inorder to use our custom helper class and property, we need to use `<color-label color="red">the red color</color-label>`.

Let's try that in our Index.cshtml page and see it.

![custom tag helper working](D:\coderethinked\jasper2\assets\posts\customtaghelper\with_red.png)

We got our custom tag helpers working now.

Let's take a look at the HTML generated

{% highlight html %}

<coloredlabel style="color:red">the red color</coloredlabel>

{% endhighlight %}

We have the new tag in the HTML i.e., `coloredlabel` this is because we've specified the output tag name to be `coloredlabel`. And the style attribute on the tag generated as any normal HTML tag would generate.

### Set Content

The `SetContent` method will set the content of the HTML tag. It just sets the content and not the HTML.

Let's modify our coloredlabel class to facilitate the Set content method.

{%highlight html%}

<color-label color="red">Should output red</color-label>

{%endhighlight%}

{% highlight csharp%}
public override void Process(TagHelperContext context, TagHelperOutput output)
​        {
​            output.TagName = "coloredlabel";

            var colorStyle = $"color:{Color}";
            output.Attributes.SetAttribute("style", colorStyle);

            if (Color == "red")
                output.Content.SetContent("Text from custom helper");
        }
{%endhighlight%}

This will just modify only the contents of the tags with red color. So, our color label will output as **Text from custom helper** (in red color).

### Pre Content and Post Content

Pre content and Post content can modify the head and tail of the custom tag helper. We can modify the contents of tag, add some custom markup around the custom tag helper like making the text bold or wrapping with some div or span.

Let's see how the appending the content at the beginning and at the end.

{%highlight csharp%}

output.PreContent.Append("Start: ");
output.PostContent.Append(" :end");

{%endhighlight%}

The above code when ran will output like this:

> Start: Text from custom helper :end (in red)

Let's try to wrap our custom tag helper content with a *div that has 2px border and blue color*.

We've to add add HTML content around our custom tag helper.

{%highlight csharp%}

public override void Process(TagHelperContext context, TagHelperOutput output)
​        {
​            output.TagName = "coloredlabel";

            var colorStyle = $"color:{Color}";
            output.Attributes.SetAttribute("style", colorStyle);

            output.PreContent.SetHtmlContent("<div style='border: 2px solid blue; padding: 5px'>");
            output.PostContent.SetHtmlContent("</div>");
        }

{%endhighlight%}

Notice how we are ending the div tag with `PostContent`.

This is how it looks like

![Custom Tag helper with wrapping PreContent and PostContent](G:\coderethinked\jasper2\assets\posts\custom tag helper\with_border.png)

Let's examine the HTML of how this is generated.

{%highlight html%}

<coloredlabel style="color:red">

<div style="border: 2px solid blue; padding: 5px">Should output red</div></coloredlabel>

{%endhighlight%}

So, we got our `<div>` element inside of `coloredlabel` tag because we've used PreContent and Postcontent. As the word says, it just the content that is going to wrap not the HTML.

### How to wrap up the custom helper? (PreElement, PostElement)

The `PreElement` and the `PostElement` will wrap the custom helper inside the element that we supply.

Let's try the same example of wrapping our custom tag helper with a div that has 2px border.

{%highlight csharp%}

public override void Process(TagHelperContext context, TagHelperOutput output)
​        {
​            output.TagName = "coloredlabel";

            var colorStyle = $"color:{Color}";
            output.Attributes.SetAttribute("style", colorStyle);

            output.PreElement.SetHtmlContent("<div style='border: 2px solid blue; padding: 5px'>");
            output.PostElement.SetHtmlContent("</div>");
        }
{%endhighlight%}

we've just replaced PreContent and PostContent it with PreElement and PostElement. Let's see the output

![Custom Tag helper with PreElement and PostElement](G:\coderethinked\jasper2\assets\posts\customtaghelper\with_border.png)

Now, the output is same as Precontent and PostContent. But, the HTML should vary. Let's see the HTML generated.

{%highlight html%}

<div style="border: 2px solid blue; padding: 5px">
    <coloredlabel style="color:red">Should output red</coloredlabel>
</div>

{%endhighlight%}

Did you see the difference here? The `<div>` tag wrapped our custom tag helper in this case.

So, it is up to the developer whether to use Pre/Post Content or to use Pre/Post Element.

### Conclusion
Custom tag helpers allows us to customize the content of the HTML by providing some useful methods to tweak the HTML and render the content dynamically.

- Tag helpers were added with an intension to make the UI developers life easy to mitigate with the HTML. As with any product, everybody will try out the custom helpers and certainly will use custom helpers. If the markup is modified with PostContent, PreContent or with PreElement, PostElement wouldn't the UI developer worry about styling the markup?

- Though the PreContent, PostContent behaved same as PreElement, PostElement. It is up to you to decide what markup you need.

- If we gave the wrong styles or wrong HTML, the markup will be rejected and only the plain HTML will be displayed. This is a good thing. Try it yourself.
