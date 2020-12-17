---
layout: post
title: "Extending Eshell Git Prompt To Provide Virtualenv Info"
date: 2020-12-17
categories: emacs eshell
permalink: extending-eshell-git-prompt-to-provide-virtualenv-info
---

![](/assets/images/posts/eshell-venv.png)

There's a package that customizes your eshell prompt, it's called [eshell-git-prompt](https://github.com/xuchunyang/eshell-git-prompt). Its pretty awesome except it doesn't show
which virtualenv I'm in, which is a big no no for me. So I ended up creating a different prompt from one of the provided ones in the package.

Before I show the code, it's important to know that I'm using [virtualenvwrapper](https://github.com/porterjamesj/virtualenvwrapper.el) to know which virtualenv I'm currently in, therefore a dependency.

Here's basically what I added to the `powerline` prompt theme
```lisp
(when venv-current-name
       (concat
        (with-read-only-face venv-current-name
          :background "#5B3758")
        (with-read-only-face segment-separator
          :foreground "#5B3758"
          :background (face-background 'eshell-git-prompt-powerline-dir-face))))
```
Small snippet to inject :)

Anyways, here's how it all looks together:
```lisp
;;; An extension of 'powerline' theme from `eshell-git-prompt`

(defmacro with-read-only-face (str &rest properties)
  "Add face PROPERTIES to STR."
  (declare (indent 1))
  `(propertize ,str 'face (list ,@properties) 'read-only t 'rear-nonsticky '(read-only)))

(defun eshell-git-prompt-powerline-venv ()
  (let ((segment-separator "\xe0b0")
        (branch            "\xe0a0")
        (detached          "\x27a6")
        (cross             "\x2718")
        dir git git-face)
    (setq dir
          (propertize
           (concat
            " "
            (unless (eshell-git-prompt-exit-success-p)
              (concat cross " "))
            (eshell-git-prompt-powerline-dir)
            " ")
           'face 'eshell-git-prompt-powerline-dir-face 'read-only t))
    (setq git
          (when (eshell-git-prompt--git-root-dir)
            (setq git-face
                  (if (eshell-git-prompt--collect-status)
                      'eshell-git-prompt-powerline-not-clean-face
                    'eshell-git-prompt-powerline-clean-face))
            (setq eshell-git-prompt-branch-name (eshell-git-prompt--branch-name))
            (propertize
                (concat " "
                        (-if-let (branch-name eshell-git-prompt-branch-name)
                            (concat branch " " branch-name)
                          (concat detached " "(eshell-git-prompt--commit-short-sha)))
                        " ")
              'face git-face 'read-only t)))
    (concat
     ;; This is the actuall important stuff, everthing else is the same
     (when venv-current-name
       (concat
        (with-read-only-face venv-current-name
          :background "#5B3758")
        (with-read-only-face segment-separator
          :foreground "#5B3758"
          :background (face-background 'eshell-git-prompt-powerline-dir-face))))
     (if git
         (concat dir
                 (with-read-only-face segment-separator
                   :foreground (face-background 'eshell-git-prompt-powerline-dir-face)
                   :background (face-background git-face))
                 git
                 (with-read-only-face segment-separator
                   :foreground (face-background git-face)))
       (concat dir
               (with-read-only-face segment-separator
                 :foreground (face-background 'eshell-git-prompt-powerline-dir-face))))
     (with-read-only-face (concat "\n" segment-separator)
       :foreground (face-background 'eshell-git-prompt-powerline-dir-face))
     (propertize "$" 'invisible t 'read-only t)
     (with-read-only-face " "))))

(defconst eshell-git-prompt-powerline-venv-regexp "^[^$\n]*\\\$ ")

(provide 'powerline-with-venv)
```
I put this somewhere in my `.emacs.d` directory and load up it via

```lisp
(use-package powerline-with-venv
  :ensure nil
  :after eshell-git-prompt ;; This is a dependency
  :load-path "lisp/themes/powerline-with-venv" ;; path to where I saved the file
  :config
  ;; Allow me to add this to the possible eshell prompt selections
  (add-to-list 'eshell-git-prompt-themes
               '(powerline-plus eshell-git-prompt-powerline-venv eshell-git-prompt-powerline-regexp))
  ;; Automatically set this as the default theme to use
  (eshell-git-prompt-use-theme 'powerline-plus))
```

It was pretty simple to do, honestly the longest time was to decided what color to use as the virtualenv part.
