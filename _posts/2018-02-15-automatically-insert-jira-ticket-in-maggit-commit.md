---
layout: post
date: 2018-02-15
title: "Automatically Insert Jira Ticket In Maggit Commit"
permalink: automatically-insert-jira-ticket-in-maggit-commit
categories: emacs
---

We use Jira where I work and I find it helpful to include the Jira issue/ticket number pre-populated within the commit message. This only works if the branch I'm working on starts with the Jira issue number. Otherwise it doesn't input anything. Here's the snippet that does so:

{% highlight elisp %}
;; If the branch has a jira ticket, also add that on the commit message
(add-hook 'git-commit-setup-hook
    '(lambda ()
        (let ((has-ticket-title (string-match "^[A-Z]+-[0-9]+"
                                    (magit-get-current-branch)))
              (words (s-split-words (magit-get-current-branch))))
          (if has-ticket-title
              (insert (format "%s-%s " (car words) (car (cdr words))))))))
{% endhighlight %}
