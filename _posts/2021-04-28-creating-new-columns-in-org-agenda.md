---
layout: post
date: 2021-04-28
title: "Creating new columns in Org agenda"
permalink: creating-new-columns-in-org-agenda
categories: emacs
---

Whenever viewing the Org agenda, you can add new "columns" to the view with the help of the `org-agenda-prefix-format` variable.
If you do a simple `C-h v org-agenda-prefix-format` in emacs, you should get a nice description of what it does and how to use it.

Here is an example on how to extend it

```lisp
;; Originally from here: https://stackoverflow.com/a/59001859/2178312
(defun gopar/get-schedule-or-deadline-if-available ()
  "Return an calendar emoji if current item doens't have a schedule or deadline.
  Otherwise return an empty string."
  (let ((scheduled (org-get-scheduled-time (point)))
        (deadline (org-get-deadline-time (point))))
    (if (not (or scheduled deadline))
        (format " ")
      "   ")))

(org-agenda-prefix-format
   '((agenda . " %i %-12:c%?-12t% s")
     (todo . " %i %-10:c %-5e %(gopar/get-schedule-or-deadline-if-available)")
     (tags . " %i %-12:c")
     (search . " %i %-12:c")))
```

In a nutshell, if I have a `TODO` item that doesn't have a schedule or deadline, then I'll add a display a simple calendar emoji, otherwise I'll just leave some space (To emulate it being empty, otherwise the spacing gets out of sync)

I also made it so that it displays the effort.

Heres a screenshot of how it looks (I use a black/white theme):
![](/assets/images/posts/org_column.png)

As you can see, I have 2 thigs that don't have a schedule/deadline and 2 of them show the estimated time it would take to complete them.
