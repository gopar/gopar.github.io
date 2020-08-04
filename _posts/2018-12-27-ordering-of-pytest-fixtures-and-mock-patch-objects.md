---
layout: post
date: 2018-12-27
title: "Ordering Of Pytest Fixtures And Mock Patch Objects"
permalink: ordering-of-pytest-fixtures-and-mock-patch-objects
categories: python pytest
---
If you're using the mock library and pytest's fixtures, then you might be confused for a second about the ordering. Mock objects go first and then pytests fixtures.

Here is an example:

```python
import mock
import os
import pytest

@pytest.fixture()
def person():
    return Person(fname='test', lname='person')

@mock.patch.object(another, 'example')
@mock.patch.object(os, 'remove')
def test_remove_person_file(mock_remove, mock_example, person):
    # Remove the persons file
    os.remove('{}-{}.txt'.format(person.fname, person.lname))

    mock_remove.assert_called_once_with('test-person.txt')
```
