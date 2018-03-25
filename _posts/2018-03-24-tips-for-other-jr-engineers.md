---
layout: post
date: 2018-03-24
title: "Tips For Other Jr Engineers"
permalink: tips-for-other-jr-engineers
categories:
---
I'm a Junior engineer and here's a couple of things I wish I knew or had more experience with before starting a job. Before I start I want to say that his is aimed for Students and other Junior Engineers that are in a similar situation (Some things you might already know or think it was obvious to know but this is my personal experience). Anyways, here it is:
- [Debugger](#debugger)
- [Logging Properly](#logging)
- [How SSH keys work and what they're for](#ssh)
- [Context Switching with projects](#projects)
- [Optimizing use of tools](#tools)
- [Code Assumptions](#code)
- [Using StackoverFlow](#stackoverflow)

## <a id='debugger'>Debugger</a>
In college I didn't use debuggers because I thought they were dumb, and instead I would sprinkle my code with print statements to figure
out what was going on. Sadly this isn't the best solution, it wasn't until I saw this video about *[pudb](https://www.youtube.com/watch?v=IEXx-AQLOBk )*
that I decided to try a debugger. I highly ask of anyone reading this who doesn't use a debugger to try it out. It has saved me so many times when
I've been stuck trying to figure out something.

## <a id='logging'>Logging Properly</a>
I was never taught to log, and the closest to logging I came was using print statements.(Not a good way by the way)
If you're using Python, I suggest reading [this article on it.](https://fangpenlin.com/posts/2012/08/26/good-logging-practice-in-python/).
I like the examples the author has in the *Write logging records everywhere with proper level* section

## <a id='ssh'>How SSH keys work and what they're for</a>
I already had an idea on how it worked and why it's important but when I was senior in college,
some of my peers didn't really know much about the topic. And it's very important. Here's a pretty
nice article on the topic from the [Archlinux community](https://wiki.archlinux.org/index.php/SSH_keys#Background)

## <a id='projects'>Context Switching with projects</a>
This skill seems to pretty hard to master even for senior developers (At least that's what my google searches was telling me).
To give a quick summary of what project context switching is, it's basically your ability to get up to speed with a project
you just switched to. Things like the code structure, patterns being used, etc.

My only advice when doing a context switching with a new project is to *take your time*. Seriously. If you're a junior,
then your team will know you won't be able to get up to speed as quickly as the other more senior members, so relax a little
and make sure to read the documentation, or anything else that can help you. After a while *you will improve*, but it *takes time*.

Also, if your context switching to a project you've worked with before, you'll be able to pick up a little faster. One thing I started
doing is to keep a notes file. I put little details about the project, or things I learned that took some time for me to digest/figure out.

## <a id='tools'>Optimizing use of tools</a>
If you spent a pretty good amount on something, it'd be pretty wise for you to optimize your usage on it.
A good place to start for optimization is your IDE or editor. I use Emacs as my primary editor and I have
configured that thing in too many ways that would bore people. One of the things I've done is make my semicolon
key output an underscore character because I was too lazy to press shift and the dash button together, but hey it works
and I've gotten faster because of it(Okay sure, debateable if faster but I'm more comfortable doing that). Go ahead an try to configure your editor/IDE the way you want it to be as proficient
as possible.

## <a id='code'>Code Assumptions</a>
Don't make assumptinos about the code or the tool that you are using. That's pretty much the main point.
Here's a story where I learned that the hard way. I spent a good amount of time trying to figure out why a call
to an api was failing. I put all the right data and auth was right. The one thing I had forgotten was a header
that wasn't set. I assumed that the library I was using was setting that for me since all the other libraries that
did the similar thing have done it before. That was a wrong *assumption*.


## <a id='stackoverflow'>Using StackoverFlow</a>
StackOverFlow is an amazing site, but sometimes the top rank answers aren't the *best/secure* answers. I've made it a habit
to read other peoples top rated answers as well and the comments from each one. You can find some interesting discussions
and learn new things you didn't know. But moral is that don't just copy the top rated answer.
