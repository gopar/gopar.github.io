---
layout: post
date: 2022-09-02
title: "Simple Language Agnostic Emacs IDE"
permalink: simple-language-agnostic-emacs-ide
categories: emacs
---

A lot of ppl are jumping in the LSP ship for their all purpose IDE, which is
fine but LSP has never been friendly to me. I always ended up having problems
with something. This led me to build a minimal but very helpful setup that will
work for many of the most used languages (Python/Javascript/Java/C++/etc).

I wanted an IDE setup within emacs that didn't involved LSP or any other server
because I kept running into problems and maintaining multiple servers for each
language is a hassle. After searching for a bit I came into the following
packages/libraries that have helped that transition to a simple but powerful IDE.

Here they are:

1. [Projectile](https://github.com/bbatsov/projectile)
2. [Dumbjump](https://github.com/jacktasia/dumb-jump)
3. [Company Mode](https://github.com/company-mode/company-mode)
4. [GUD](https://www.gnu.org/software/emacs/manual/html_node/emacs/Debuggers.html)

The reasoning for the follwing are below:

- Projectile: This a project management and navigation package. Think of it as
  your sidebar view of files in a typical IDE plus with the ability of running
  commands on the entire project.

- Dumbjump: This is arguably the most essential feature, the ability to jump to
  definition. This package is nicely well written and works amazing. Best of
  all, works on all major languages and has no dependencies.

- Company Mode: This is what I use for auto code completion. It's not hooked up
  to an engine but instead it simply re-uses words that I've used throughout any
  buffers I've opened. Its a "smart" dumb auto code completion. Works for my purpose

- GUD: Built in debugger library in emacs. It comes with a few debuggers such as
  `pdb` which I'm constantly using. It will probably come with a debugger you
  need. If not, then it might be worth checking out
  https://github.com/realgud/realgud.
