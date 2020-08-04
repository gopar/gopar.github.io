---
layout: post
date: 2017-12-24
title: "Fixtures From Type Annotations"
permalink: fixtures-from-type-annotations
categories: python
---

I read [this blog post](http://ominian.com/2017/11/06/python-3-abusing-annotations/) about converting function/method arguments into what their type annotations mentioned they would be. You can read more from that link, but pretty much I got an idea that someone should be able to create fixtures from type annotations. Anyways, here's a proof of concept just using the basic built-in types in Python.

```python
from typing import get_type_hints


def create_fixture(klass):
    type_signatures = get_type_hints(klass.__init__)
    if 'return' in type_signatures:
        del type_signatures['return']

    kwargs = {}
    for name, signature in type_signatures.items():
        # instead of creating empty string, use this one as a placeholder
        result = 'string' if signature == str else signature()
        kwargs[name] = result

    return klass(**kwargs)


class Home:
    def __init__(self, address: str, zipcode: int, floaty: float, for_sale: bool) -> None:
        self.address = address
        self.zipcode = zipcode
        self.floaty = floaty
        self.for_sale = for_sale


home = create_fixture(Home)
print(home.address, home.zipcode, home.floaty, home.for_sale)
# output
# string 0 0.0 False
```

Pretty cool stuff and it was really simple to do. I decided to try and create my own library to do this. It's currently hosted on Github and called [Pyfta](https://github.com/gopar/pyfta). I need to dig more into the `typing` module to be able to support things like `List[Union[Dict[str,int], int]]`. We'll see how far I get
