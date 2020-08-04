---
layout: post
date: 2015-03-19
title: "Mimicking Constants In Python"
categories: python
permalink: mimicking-constants-in-python
---

**Spoiler**: The reason this is not called *Creating Constants in Python* is because there is no way of creating *true* constants just beacuse of the way Python is. But that doesn't stop us from mimicking that behaviour in attributes.

Most people that have messed around with Python have probably already noticed that there are no constant variables. To some people, like for my friend [@Miguel](https://twitter.com/bodhidweller), this might seem weird or awkward. To give an example, we can look at Python's *math* module, in the module you overwrite some values that in other languages would have been made into constants. Here is what I mean:

```python
>>> import math
>>> math.pi
3.141592653589793
>>> math.pi = 123
>>> math.pi
123
```

See? If `pi` was a constant, then trying to change the value should of raised an error but in this case it just accepted its new value. Now some people would say that if the person was naive or ignorant enough to change the value, then hey, it's their problem and not yours. In a sense I agree, but in this case with `pi` being all lower case instead of the traditional *ALL_CAPS* constant definition, it can be a bit confusing (well for some people at least) and even more if *some* people don't know that *pi* is supposed to be a constant!

## To Mimic Constants
As of now I can only think of 2 ways of achieving this, using Pythons [property](http://www.programiz.com/python-programming/property) function and playing with [Magic Methods](http://www.rafekettler.com/magicmethods.html). I'll first go over this with properties.

Here is an example on how might a class be made that mimics constant attributes.

```python
class Math(object):
    def __init__(self):
        self._pi = 3.14

    @property
    def pi(self):
        return self._pi

    @pi.setter
    def pi(self, value):
        """ Do nothing when 'pi' is being assigned a new value """"
        pass
```

If we run this bit of code, it'll go something like this.

```python
>>> a = Math()
>>> a.pi
90
>>> a.pi = 0
>>> a.pi
90
```

Now we can't change `pi`! But the downfall to this bit of code is that someone is able to do the following.

```python
>>> a._pi = 0
>>> a.pi
0
```

The way to fix this is to return 3.14 from the `pi` method directly instead of returning `self._pi`. Another thing that might be interesting to do is to add attributes dynamically to the object. The way we can achieve that using properties is by doing the following.

```python
# Tie attrubute to the **class** and not the instance
>>> Math.e = property(fget=lambda self: 2.718, fset=lambda self, value: None)
>>> a.e
2.718
>>> a.e = 90
>>> a.e
2.718
```

Problem fixed, the only thing with this is that the attributes `e` and `pi` are tied to the **class** instead of an instance.

Next is doing the same but with *Magic Methods*. First we would have to override *\_\_setattr_\_\_*. In a nutshell, the *\_\_setattr_\_\_* method is called internally whenever you assign a value to a variable, for example `var = 90`. This method accepts 2 arguments, a name and a value. The `name` is the variable you want to change, and `value` is well the new value it will hold. Here is an example of how this would work.

```python
class PyConstObj(object):
    """ Implementation of a constant object in Python """
    # --------------------------------------------------------------------------
    def __init__(self, **kwargs):
        for name, value in kwargs.items():
            super(PyConstObj, self).__setattr__(name, value)

    # --------------------------------------------------------------------------
    def __setattr__(self, name, val):
        """ When trying to set a new value to the constant object """
        # If variable already exists in instance
        if name in self.__dict__:
            raise ValueError("Cannot change value of a constant")
        else:
            super(PyConstObj, self).__setattr__(name, val)
```

If we create an instance of that class and try to change one of its attributes we get an error like below.

```python
>>> math = PyConst(value=90, pi=3.14)
>>> math.pi
3.14
>>> math.pi = 100
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/daniel/Desktop/test.py", line 10, in __setattr__
    raise ValueError("Cannot change value")
ValueError: Cannot change value
```

We can also dynamically add attributes without adding any extra code.

```python
>>> math.love = 90
>>> math.love
90
```

These attributes are bound to the instance, unlike the property example. With this, the instance attributes act like constants without really being one. I made a small [github project](https://github.com/gopar/PyConst/) out this for those wanting to see a bit more but this basically about sums it.
