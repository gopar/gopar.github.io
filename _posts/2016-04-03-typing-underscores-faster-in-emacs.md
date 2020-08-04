---
layout: post
date: 2016-04-03
title: "Typing Underscores Faster In Emacs"
permalink: typing-underscores-faster-in-emacs
categories: emacs
---

I recently saw a [Twitter post](https://twitter.com/johnkitchin/status/713448147360546816) that showed how to type the "_" (underscore) faster. I really liked the idea and tried it out. Sure enough, I loved it but the issue was that `key-chord` was slow and I didn't want to install a package just to use it *once*. So, I decided to try to create one without the need of `key-chord` and I got something running that works for me.

```common-lisp
(defun python-underscore (arg)
  (interactive "P")
  (if arg
      (insert ";")
    (insert "_")))

(define-key elpy-mode-map (kbd ";") 'python-underscore)
```

If you type `;` you get `_` and if you type `C-u ;` you'll get your beloved `;` back. Pretty neat if you ask me.
