---
layout: post
date: 2015-03-15
title: "VMware Workstation, Ubuntu 14.04, And The Keyboard Problem"
categories:
permalink: vmware-workstation-11-and-ubuntu-14-04-keyboard/
---

I have VMware Workstation version 11 and Gnome Ubuntu 14.04. The problem I have is that whenever I focus to a VM and then focus out to the host machine, for some reason all my main keyboard shortcuts don't work! For example Ctrl, Alt, Super, and other keyboard bindings no longer function, heck they don't do anything at all! The only thing I was able to find about this, was this [small post](http://nthrbldyblg.blogspot.com/2008/06/vmware-and-fubar-keyboard-effect.html). Thankfully, this helped me figure out what was going on, but I had to do something different than the directions given there.

Go read the post and try out the solutions given there, if they work then great! But if your luck is just like mine, then it probably din't. That's Okay, hopefully this post will help you out.

First thing is you have to install `x11-xkb-utils`. This is from the post I mentioned earlier.

```bash
sudo apt-get install x11-xkb-utils
```

Great, now the only thing you have to do is run the following command on the terminal every time you re-focus to the host machine:

```bash
setxkbmap
```

Unfortunately, this is kinda a drag compared to what is said in the earlier post because you have to do this *every time* you focus on your host machine. But it does restore all keyboard keys and shortcuts which was the goal. I've been trying to see if there's any way I can hook vmware to run a command when ever I get out of the VM but so far no luck. If you know how to do this, or know of a different solution, please comment below.
