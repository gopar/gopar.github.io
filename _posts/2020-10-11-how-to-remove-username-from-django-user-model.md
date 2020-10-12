---
layout: post
title: "How to Remove Username from Django User Model"
date: 2020-10-11
categories: python django
permalink: how-to-remove-username-from-django-user-model
---

If you want to have users authenticate via email (or some other attribute) instead of username then theres a few things you'll have to do.

First you need to create a custom user model and in it set `username = None`, and set `USERNAME_FIELD = "email"` or any other attribute you want to be used to login. Also whatever attribute you choose, **has to be** set to `unique=True`

```python
# models.py
from django.contrib.auth.models import AbstractUser
from django.db import models

class User(AbstractUser):
    REQUIRED_FIELDS = []
    USERNAME_FIELD = "email"
    username = None
    email = models.EmailField("email address", blank=False, null=False, unique=True)
```

You'll still need create an [Object Manager](https://docs.djangoproject.com/en/3.1/topics/db/managers/) for the new custom user class since we've changed how django expects objects to be created. I subclassed the default user object manager and just override some methods like so:

```python
from django.contrib.auth.models import UserManager as BaseUserManager

class UserManager(BaseUserManager):
    """ User Manager that knows how to create users via email instead of username """
    def _create_user(self, email, password, **extra_fields):
        email = self.normalize_email(email)
        user = self.model(email=email, **extra_fields)
        user.set_password(password)
        user.save(using=self._db)
        return user

    def create_superuser(self, email=None, password=None, **extra_fields):
        extra_fields.setdefault("is_staff", True)
        extra_fields.setdefault("is_superuser", True)

        if extra_fields.get("is_staff") is not True:
            raise ValueError("Superuser must have is_staff=True.")
        if extra_fields.get("is_superuser") is not True:
            raise ValueError("Superuser must have is_superuser=True.")

        return self._create_user(email, password, **extra_fields)

    def create_user(self, email=None, password=None, **extra_fields):
        extra_fields.setdefault("is_staff", False)
        extra_fields.setdefault("is_superuser", False)
        return self._create_user(email, password, **extra_fields)


class User(AbstractUser):
    objects = UserManager()  # Don't forget this part!!
    ....
```

Check out how the default User Manager was creating objects to see why we needed this change.

Another thing we have to do is update `AUTH_USER_MODEL` in our `settings.py` ([See documentation for how to do that, but should be simple](https://docs.djangoproject.com/en/3.1/topics/auth/customizing/#substituting-a-custom-user-model))


Final step is to run the migrations
```shell
$ python manage.py makemigrations
...
$ python manage.py migrate
...
$ python manage.py createsuperuser
....
$ python manage.py runserver
```

You should be able to login with email instead of username
