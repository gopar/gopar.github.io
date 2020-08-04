---
layout: post
date: 2015-12-16
title: "Setting Emacs Transparency"
permalink: setting-emacs-transparency
categories: emacs
---

This is a simple function that I have in my `init.el` to control the transparency of emacs.

```common-lisp
(defun set-transparency (onfocus notfocus)
  "Set the transparency for emacs. Values are from 0 - 100"
  (interactive "nOn Focus: \nnOn Unfocus: ")
  (set-frame-parameter (selected-frame) 'alpha (list onfocus notfocus)))
```

I don't use this function that often so I don't have it tied to any key. I just call it with `M-x set-transparency`.
