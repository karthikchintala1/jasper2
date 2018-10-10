---
layout: "post"
title: "Overriding jQuery default events"
date: "2018-10-10 10:47"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: jQuery method Overriding
cover: 'assets/covers/'
description: How to override the default jQuery event listeners. In this post, we'll customize the logic so that we can override the default jQuery click event.
---

In this post, we'll see how to override the jQuery click event.

Steps:
1. Find the original method that we can override and store into a global variable    
2. Define the overriding methods
3. Have your custom logic in the method.

`jQuery.fn` will have all the list of events that you can override. Here is the list from developer tools

![Overriding jQuery default events: list of events](assets/posts/method_override/list_of_events.png)

{%highlight js%}
(function(){
    //store the original event to call later on
    var originalEvent = jQuery.fn.click;
    //override the method
    jQuery.fn.click = function(){
        //do your thing
        console.log("from overridden method");

        //call the original event again to have the click event working
        originalEvent.apply(this, arguments);
    }
})();
{%endhighlight%}
