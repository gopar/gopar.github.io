---
layout: post
title: "Eshell CD tricks"
date: 2020-11-27
categories: emacs eshell
permalink: eshell-cd-tricks
---

Eshell's built in command `cd` has a pretty nice feature,
which is keeping track of the directories visited and being able to jump to a directory
matching the directory stack history.

You can see the directory stack via `$ cd =` and then either choose a number or directory to jump to
(eg `cd =foo` or `cd -3`) both will jump to the `foo` directory

eg:

```bash
$ cd =
0: /Users/gopar
1: /Users/gopar/.emacs.d
2: /Users/gopar/projs
3: /Users/gopar/.emacs.d/org/work
5: /Users/gopar
6: /Users/gopar/.emacs.d/org
7: /Users/gopar/foo
$ cd =foo # cd's into directory foo
$ cd -7 # also cd's into foo
```

----------

Another cool thing that it has is that you don't have to type `cd path/dir` but simply `path/dir` in the prompt to cd into that directory. This works by checking if the input is a path, if not then fallback to using it as a command.

Eshells CD command is a pretty simple easy convinient tool :)
