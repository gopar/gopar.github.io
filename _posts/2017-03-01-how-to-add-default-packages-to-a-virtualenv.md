---
layout: post
date: 2017-03-01
title: "How To Add Default Packages To A Virtualenv"
permalink: how-to-add-default-packages-to-a-virtualenv
categories: python
---

If you ever need certain packages to be installed by default when creating a virtualenv through virtualenvwrapper, then this post might help you out. Now there's a couple of ways of doing what I'm about to show but this approach is the one I like the best.

First you need to install the packages you desire into a directory, I chose `~/.virtualenvs/deps`. I work with both python 2 and 3 so I have another directory called `~/.virtualenvs/deps3` for the python 3 stuff.

Next with the correct version of `pip` run

```bash
pip install -U --target ~/.virtualenvs/deps ...
```

Replace the `...` with the desired libraries. Do this for python2 and python3 if you want to.

Next, in `~/.virtualenv/postmkvirtualenv` add the following:

```python
# find directory
SITEDIR=$(virtualenvwrapper_get_site_packages_dir)
PYVER=$(virtualenvwrapper_get_python_version)

# create new .pth file with our path depending of python version
if [[ $PYVER == 3* ]];
then
    echo "/Users/gopar/.virtualenvs/elpydeps3/" > "$SITEDIR/extra.pth";
else
    echo "/Users/gopar/.virtualenvs/elpydeps/" > "$SITEDIR/extra.pth";
fi
```

This will take care of our default packages every time we create a new virtualenv. Of course, you'll need to manually update the packages but for me it doesn't really matter if they're up to date or not.
