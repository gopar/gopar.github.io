---
layout: post
date: 2015-04-22
title: "Creating And Deploying Org Mode Jekyll Site To Github Pages"
permalink: creating-and-deploying-org-mode-jekyll-site-to-github-pages
categories: emacs
---

We'll go over how to create a [Jekyll](http://jekyllrb.com/) site using [Org-mode](http://orgmode.org/) and how to deploy it to [Github Pages](https://pages.github.com/)! I won't go over styling and creating a nice Jekyll site, just building a bare bones basic one, and deploying to GitHub.

## Folder Structure
First off, create a root directory, I'll name it `mysite`. Now, once you have done that, mimic the following folder structure:
{% highlight bash %}
mysite/ # This is the folder you just created
├── assets
│   ├── css
│   ├── images
│   └── js
├── _includes
├── _layout
├── org
│   └── _posts
└── _posts
{% endhighlight %}

Ideally, we would want one folder for org files, and another for the Jekyll site. Unfortunately, the way GitHub is, it does [not allow for this type of setup](https://help.github.com/articles/using-jekyll-with-pages/#configuration-overrides) and only checks in the top directory for the files. The price we pay for free web hosting.

The folders *_includes, _layouts*, and *assets* are just for styling and templating. I won't go into detail about this, since this post is just how to get your Jekyll site to GitHub.

## Org-mode Hack
Next is updating some things in your `init.el` file. Just copy, paste then modify to meet your needs:

{% highlight  common-lisp %}
;; For jekyll
(setq org-publish-project-alist
    '(("org-mysite"
       ;; Path to your org files.
       :base-directory "path/to/proj/mysite/org"
       :base-extension "org"

       ;; Path to your Jekyll project.
       :publishing-directory "path/to/proj/mysite/"
       :recursive t
       :publishing-function org-publish-org-to-html
       :headline-levels 4
       :html-extension "html"
       :body-only t ;; Only export section between <body> </body>
       :table-of-contents nil)

      ("org-static-mysite"
       :base-directory "path/to/proj/mysite/org/"
       :base-extension "css\\|js\\|png\\|jpg\\|gif\\|pdf\\|mp3\\|ogg\\|swf\\|php"
       :publishing-directory "path/to/proj/mysite/"
       :recursive t
       :publishing-function org-publish-attachment
       :table-of-contents nil)

      ("mysite" :components ("org-mysite" "org-static-mysite"))))
{% endhighlight %}

I got this bit from the [org-mode](http://orgmode.org/worg/org-tutorials/org-jekyll.html) site. I tweaked it a bit. Once you have done that, evaluate the statement, reload the file, or restart emacs to make the change take effect.

## Creating Pages
Alright, we're one step closer to finishing! Next, is creating the configuration file for Jekyll to use once we start its server. Create a file called `_config.yml`, and just paste this line inside: `exclude: [org/]`. Without this line, Jekyll will make the files in *org* directory visible to anyone, and we don't want that. If you want to see all available options, check out [Jekyll's documentation](http://jekyllrb.com/docs/configuration/).

Next step is to create the `index.html` file but since we're using Org, we'll call it `index.org`. Create the file inside the *org* directory and just put the following for now:

```
#+BEGIN_HTML
---
title: YOUR AWESOME TITLE HERE
---
#+END_HTML

* Hello World!

  This is the index page
```

The lines between the HTML tags are *needed* by Jekyll to be able to do whatever it needs to do. You would usually declare what layouts you want to use and other things in there. This is all we need to be able to test and run our new site!

Before we being testing and seeing our site locally, we must first convert our files to html. This is where the org hack comes in handy. Do `C-c C-e` while in `index.org`. You should see a plethora of menu options, ignore all of them and press `X` (uppercase), next enter `mysite`. You should see the mini buffer printing out some info. Once it's done, we'll move to the next section. The complete command would be `C-c C-e X mysite`

## Running Your Site Locally
Okay, go to your terminal and go to your project root directory. Next, run `jekyll serve`. The server should start and tell you that it's running on 127.0.0.1:4000. If you go there, you should see our simple little index file. There you go, Website completed! Not really, but you get the point.

## Deploying to GitHub (Finally)
You are now ready to show off your site! First thing you need to do is create an empty repository (Don't add a readme or anything) in github with the following format: `USERNAME.github.io`. My user name is *gopar*, so mine would be `gopar.github.io`. Once the repo is created, just copy and run these commands from the **root** directory of your project. In this case, our root directory is `mysite`.

{% highlight bash %}
echo "# USERNAME.github.io" >> README.md
echo "_site/" > .gitignore
git init
git add .
git commit -am "first commit"
git remote add origin https://github.com/USERNAME/USERNAME.github.io.git
git push -u origin master
{% endhighlight %}

If you now go to your browser and type `USERNAME.github.io`, you should see your new website! If you want to see an example repo of this, check out the [emacs meetup](https://github.com/emacsmc/emacsmc.github.io) I created. Hope this helps :)
