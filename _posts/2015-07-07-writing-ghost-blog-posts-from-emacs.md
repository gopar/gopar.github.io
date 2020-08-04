---
layout: post
date: 2015-07-07
title: "Writing Ghost Blog Posts From Emacs"
permalink: writing-ghost-blog-posts-from-emacs
categories: emacs
---

I use [Ghost](https://ghost.org/) to blog and I have to say that the people behind it are amazing. They did an awesome job with it since it's so easy and simple to do everything! But as much as I love ghost, I can't stand the fact that I have to **leave** emacs to write a simple blog post. So what would any emacs lover do? Make it so that you write it in emacs!

## Setting Up ##

Since Ghost uses markdown to write posts, the first thing I did was install `markdown-mode` from MELPA. Alright! step one finished! Next I installed
`olivetti` that is also in MELPA. That package is pretty sweet, it's a minor mode for a legit writing environment. Try it out, you'll fall in love with it too.

Olivetti comes with a default width of 80 (I think), I didn't like that so I changed it to 90 like this:

```common-lisp
(setq olivetti-body-width 90)
```

Mess around till you find what you like :)

The next obvious thing that was missing was spell checking. Simply calling `M-x flyspell-mode` was enough to fix this. You can jump to the next misspelled word by pressing `C-,` and then calling `C-c $` to give suggestions to fix the typo. Of course with the power of emacs, you can rebind them to anything else, I've just been too lazy to do it.

Alright, since calling `flyspell` and `olivetti` every time we load a markdown file would be annoying, I just added a hook:

```common-lisp
(add-hook 'markdown-mode-hook
          '(lambda ()
            (set (make-local-variable 'company-backends) '(company-dabbrev))
            (linum-mode -1) ;; There's a bug that won't let olivetti work with this.
            (olivetti-mode)
            (flyspell-mode 1)))
```

Okay, so that little lambda had some extra stuff. For example, I'm setting `company` to only use `company-dabbrev` for completion, this is useful because it becomes another auto-complete function but just for text.

The next bit is for some people, like me, who use `linum-mode` a lot. Sadly, Olivetti doesn't play nice with line numbers so we have to turn it off.

## Real Time Preview ##

So we have a nice setup for writing posts, but if we want to see how our post would look like we have to copy and paste what we have to ghost's editor and then click the preview button. What a **pain**! To work around this, I found [`livedown`](https://github.com/shime/emacs-livedown) but before we can start using it, we need to have npm and nodejs installed. I did this by running the following command:

```bash
sudo apt-get install nodejs npm
```

After, I followed the instructions [here to be able to run it without using sudo](http://competa.com/blog/2014/12/how-to-run-npm-without-sudo/). If you don't feel comfortable using that site, you can [use these scripts](https://gist.github.com/isaacs/579814). Apparently, installing using those is the correct way of doing it. For me it didn't work, so lets hope your luck is better than mine.

Note: If you run into some error about node not being found, then just do this:

```bash
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

It just redirects it to be able to find node.

Cool, cool, we have node and npm setup. Next, is to actually install `livedown`. Run

```bash
npm install -g livedown
```

Installed! Bam! Alright, alright calm down peeps, we're still not done. Configure livedown to the way you want it. Sadly, once again, the package didn't work for me. I ended up calling livedown manually from the terminal. It's all good, I don't mind as long as I can see my markdown in real time.

Time for the another step that I have for writing posts. I use [Hydra](https://github.com/abo-abo/hydra/), a neat package that simplifies the calling of related key-bindings. I just grabbed the snippet that was already provided in it's GitHub wiki.

```common-lisp
(defhydra dh-hydra-markdown-mode (:hint nil)
  "
Formatting        C-c C-s    _s_: bold          _e_: italic     _b_: blockquote   _p_: pre-formatted    _c_: code

Headings          C-c C-t    _h_: automatic     _1_: h1         _2_: h2           _3_: h3               _4_: h4

Lists             C-c C-x    _m_: insert item

Demote/Promote    C-c C-x    _l_: promote       _r_: demote     _u_: move up      _d_: move down

Links, footnotes  C-c C-a    _L_: link          _U_: uri        _F_: footnote     _W_: wiki-link      _R_: reference

"

  ("s" markdown-insert-bold)
  ("e" markdown-insert-italic)
  ("b" markdown-insert-blockquote :color blue)
  ("p" markdown-insert-pre :color blue)
  ("c" markdown-insert-code)

  ("h" markdown-insert-header-dwim)
  ("1" markdown-insert-header-atx-1)
  ("2" markdown-insert-header-atx-2)
  ("3" markdown-insert-header-atx-3)
  ("4" markdown-insert-header-atx-4)

  ("m" markdown-insert-list-item)

  ("l" markdown-promote)
  ("r" markdown-demote)
  ("d" markdown-move-down)
  ("u" markdown-move-up)

  ("L" markdown-insert-link :color blue)
  ("U" markdown-insert-uri :color blue)
  ("F" markdown-insert-footnote :color blue)
  ("W" markdown-insert-wiki-link :color blue)
  ("R" markdown-insert-reference-link-dwim :color blue))

(require 'markdown-mode)
(define-key markdown-mode-map (kbd "C-c m") 'dh-hydra-markdown-mode/body)
```

I make it so that the key-binding is only available when I'm in markdown mode.

The final thing that I do once I'm finished writing, I make a call to `replace-string` and I replace "~~~" with "```", since ghost uses that instead of the tilde. Now, I just copy and paste it to ghost and I'm good to go!

## For the Future ##
The only thing lacking right now, is to be able to post right from emacs (that would be the dream wouldn't it?) As of right now there's no way to do this easily **but** the ghost team *is* currently working on an [API](http://api.ghost.org/v0.1-alpha/docs) that would make this possible!! Hopefully someone will write a ghost package, hecks *you* might be the one to do it! I suck at elisp so I'm not even going to try.
