---
layout: post
date: 2017-01-03
title: "Add Disqus Comments Without Customizing Ghost Theme"
permalink: add-disqus-comments-without-customizing-ghost-theme
categories:
---

I recently moved back to [ghost.org](http://www.ghost.org) as my blogging platform. I was sad to find out that to use Disquis you need to customize your theme. I didn't want to do that cause I'm lazy so I just decided to make it all work with javascript that you can inject into your footer themes. (You can do this by going to your Ghost Admin page and clicking on the `Code Injection` tab)

Anyways, here's the snippet of code that achieved this
```javascript
window.onload = function() {
      if(jQuery.inArray(window.location.pathname,["/", "/aboutme/", "/resources/"]) < 0 &&
        !window.location.pathname.includes("/page/")) {
          var footer = document.getElementsByTagName("footer")[0];
          var div = document.createElement("div");
          div.id = "disqus_thread";
          footer.appendChild(div);
          // Add Universal Disqus code here
        }
}
```

I'm pretty much just checking that the comments won't show up in places where I don't want it to, otherwise add the comments. Next, just go do Disqus and add the universal code that they give you. Just add in the javascript bit and your good to go.

Of course this approach isn't safe, mainly because the default Ghost theme is continually being updated and it can break any moment but I'll take my chances. I mean, how many footers can a page have?
