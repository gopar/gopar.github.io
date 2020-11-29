---
layout: post
title: "Making Eshell tell jokes"
date: 2020-11-28
categories: emacs eshell
permalink: making-eshell-tell-jokes
---

![](/assets/images/posts/eshell-joke.png)

Whenever you start eshell, there is a banner that welcomes you. I decided I didn't like having the same message everytime so I hooked up eshell to query some apis to return a joke on eshell startup.
Before I show how to do that, you need to understand that eshell loads up modules (list of modules are in `eshell-modules-list`), and in that list there is a module called `eshell-banner`  which is
in charge of displaying the banner on startup (who would of guessed). With that in mind, we can do the following:

```lisp
(use-package em-banner
  :defer t
  :ensure nil
  :custom
  (eshell-banner-message  '(funcall (nth (random (length gopar/request-jokes)) gopar/request-jokes))))
```

_Note_: I am using [`use-package`](https://github.com/jwiegley/use-package) but same functionality should be available with the use of `eval-after-load`

What I'm doing above is setting `eshell-banner-message` (which can be any s-exp) to a function that returns a string (the joke).
The function itself is randomly selecting an element from `gopar/request-jokes` (which we will define in moment), once it has selected an element, it will call it (all the elements in the list are functions)

Here is how I'm defining the variables and how I'm querying the apis

```lisp
(use-package request
  :defer t
  :ensure t
  :commands request
  :custom
  (request-timeout .5)
  (request-message-level -1)
  (request-log-level -1)
  :init
  (defun gopar/request-chuck-norris-joke ()
    "Return a chuck norris joke"
    (interactive)
    (concat (assoc-default 'value (request-response-data (request
                                                           "https://api.chucknorris.io/jokes/random"
                                                           :parser 'json-read
                                                           :headers '(("Accept" . "application/json"))
                                                           :sync t))) "\n\n"))

  (defun gopar/request-dad-joke ()
    "Return a dad joke"
    (interactive)
    (concat (assoc-default 'joke (request-response-data (request
                                                          "https://icanhazdadjoke.com/"
                                                          :parser 'json-read
                                                          :headers '(("Accept" . "application/json"))
                                                          :sync t))) "\n\n"))
  (setq gopar/request-jokes '(gopar/request-chuck-norris-joke gopar/request-dad-joke)))
```

First thing to notice is that I am using [`request`](https://github.com/tkf/emacs-request) which allows me to effortlessly make network requests \o/
Next, you can see I have 2 functions (`gopar/request-dad-joke` and `gopar/request-chuck-norris-joke`) that return a joke from their apis, and am storing both
those functions in `gopar/request-jokes` (which is what eshell banner is using).

One thing to notice is that I am using the `sync` parameter in the request call.
When using `sync`, the network call is synchronous which means that emacs will be unresponsive until the network call finishes. To me this isn't a problem since I've set the timeout to half a second (It's never taken more than that for me)

Lastly, I'm not doing any error handling. So if something blows up when doing a network call, eshell will break and you'll have to close it and call it again. Haven't had a need to implement it, but if you're up to it, you can add that in :)
