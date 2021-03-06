---
layout: post
date: 2015-02-16
title: "Installing Vala And GTK Development Library"
categories: vala
permalink: installing-vala-and-gtk-development-library
---
This post is to show how to install [Vala](https://wiki.gnome.org/Projects/Vala) and test it out. Vala is a programming language made for GNOME developers to make their life easier. Well anyways to install it, go to your terminal and run the following:

Add the PPA:

```bash
sudo add-apt-repository ppa:vala-team
```
Update:

```bash
sudo apt-get update
```
Install: (Install the most current version for you, for me it's the following)

```bash
sudo apt-get install valac-0.28
```
Check that you have it:

```bash
valac --version
```
You should get: (Depending on what version you installed)

```bash
Vala 0.27.1
```

Sweet! We have it installed. Next, if you want to use Vala to create GUI's (which you most likely are), you need to install the development files for the GTK library. The library to install is `libgtk-3-dev`, that is the dev files for GTK+3. Again go to your terminal and do the following.

Install the library:

```bash
sudo apt-get install libgtk-3-dev
```

There you go! You have everything you need to start making GUI's!
