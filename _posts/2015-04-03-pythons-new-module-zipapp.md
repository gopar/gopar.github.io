---
layout: post
date: 2015-04-03
title: "Python's New Module: Zipapp"
categories: python
permalink: pythons-new-module-zipapp
---

[Zipapp](https://docs.python.org/dev/library/zipapp.html) is a new module that is coming out in Python 3.5 (As of this writing it's in alpha stage 3). The purpose of this module is to make a single standalone executable package from multiple Python files.

Python has already had the ability to do this by calling the interpreter on a directory or an archive, such as a `zip`, that contained a `__main__.py` file. What zipapp allows is to make that process simpler and basically create a new standard/practice where you would use `pyz` or `pyzw` to let people know it's an executable. `pyz` packages are for meant for console based applications while `pyzw` are for windowed/GUI applications  and hide the console when running. What makes `zipapp` different from other packaging formats, such as wheel, is that it's ["specifically designed for standalone use, without needing to be installed"](https://www.python.org/dev/peps/pep-0441/#faq). Also, one cool thing that it allows, is that it can also be executed by older Python versions. One thing that you should know is that the executeable does **not** self contain 3rd party packages.

We'll go over how to create an executeable, but first create a directory with the following structure.

```bash
app/
└── __main__.py
```

Inside of `__main__.py` we will have the following.

```python
import sys
if sys.platform.startswith("win"):
    import tkinter
else:
    # For some reason on Linux, the `T` is capitalized
    import Tkinter as tkinter

def main():
    app = tkinter.Tk()
    tkinter.mainloop()

if __name__ == "__main__":
    main()
```

On Windows, `tkinter` automatically comes with the installation of Python, on Linux you would most likely have to install it. If you're on Ubuntu you can just run `sudo apt-get install python3-tk`.

This script just shows a window, thats about it, nothting fancy. Now if we package the app using `zipapp`, the command would be:

```bash
python -m zipapp app
```

This will ouput a file called `app.pyz`. To run your newly created package, all you have to do is type:

```bash
python app.pyz
```

Now what if you don't want to create a `__main__.py` file? Well you can let `zipapp` take of that, for example lets say that instead of naming the `__main__.py` you named it `blah.py`. The command you would run would be:

```bash
python -m zipapp app -m "blah:main"
```

The parameter `-m "blah:main"` is basically saying "Hey, inside the `app` directory there is going to be a file called `blah.py` and inside that file I want you to call the `main` function to start the program.".

Now if the file that you needed to start was inside a package, lets say in a package called "awesome", you would do:

```bash
python -m zipapp app -m "awesome.blah:main"
```

Next is how to rename your output to whatever you'd like, for example we'd like a package with the extension `pyzw` since this is a windowed application. To accomplish this we would run:

```bash
python -m zipapp app -m "blah:main" -o app.pyzw
```

If you'd wanted to, you could of renamed the output to whatever you'd like such as `tada.pyzw` or whatever.

When you are creating a package you can specify what interpreter to use when running the app with `-p`. Heres an example:

```bash
python -m zipapp myapp.pyzw -p "/usr/bin/env python"
```

And to figure out what interpreter a package is using, all you have to do is this:

```bash
python -m zipapp myapp.pyzw --info
```

Sweet! The library is pretty straigh forward to use, and it also comes with it's own small [API](https://docs.python.org/dev/library/zipapp.html#python-api) that you can use in Python.
