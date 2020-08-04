---
layout: post
date: 2017-06-17
title: "Unittest equivalent of Pytest's Parametrize"
permalink: unittest-equivalent-of-pytests-parametrize
categories: python
---

I'm currently using the `unittest` framework for writing tests. One thing I miss is being able to use the `@pytest.mark.parametrize` decorator to test out different inputs. If you know about this feature, then you know why it's so useful. Luckily, `unittest` has a similar feature that starting working since Python 3.4, it's called subtests.

This is the example straight from the [Python Docs](https://docs.python.org/3/library/unittest.html#distinguishing-test-iterations-using-subtests):

```python
class NumbersTest(unittest.TestCase):

    def test_even(self):
        """
        Test that numbers between 0 and 5 are all even.
        """
        for i in range(0, 6):
            with self.subTest(i=i):
                self.assertEqual(i % 2, 0)
```

And here is it's output (Again from the Python Docs):

```python
======================================================================
FAIL: test_even (__main__.NumbersTest) (i=1)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "subtests.py", line 32, in test_even
    self.assertEqual(i % 2, 0)
AssertionError: 1 != 0

======================================================================
FAIL: test_even (__main__.NumbersTest) (i=3)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "subtests.py", line 32, in test_even
    self.assertEqual(i % 2, 0)
AssertionError: 1 != 0

======================================================================
FAIL: test_even (__main__.NumbersTest) (i=5)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "subtests.py", line 32, in test_even
    self.assertEqual(i % 2, 0)
AssertionError: 1 != 0
```
