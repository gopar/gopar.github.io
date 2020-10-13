---
layout: post
title: "How to Remove Username from Django Rest Auth Registration and login API"
date: 2020-10-12
categories: python django
permalink: how-to-remove-username-from-django-rest-auth-registration-and-login-api
---

If you want to remove the `username` field from the Login/Register api endpoints then you'll realize this is rather easy.

I've assumed that you've follow the [registration instructions on the documentation so far](https://django-rest-auth.readthedocs.io/en/latest/installation.html#registration-optional)


First create two new serializers, which will be the ones that we'll use in the register/login endpoints.
I created a file called `serializers.py` in a folder named `helper` which will hold things that I'm overwriting from 3rd parties.


```python
from dj_rest_auth.registration.serializers import RegisterSerializer
from dj_rest_auth.serializers import LoginSerializer
from rest_framework import serializers


class CustomRegisterSerializer(RegisterSerializer):
    """Use default serializer except don't user username"""

    username = None

    def get_cleaned_data(self):
        return {
            "password1": self.validated_data.get("password1", ""),
            "email": self.validated_data.get("email", ""),
        }


class CustomLoginSerializer(LoginSerializer):
    """Use default serializer except don't user username"""

    username = None

```

Second step is tell django to use these new serializers and to forget about using the `username` field for anything. Go to `settings.py` file and add these

```python
### Django AllAuth settings
# Telling Allauth that we will NOT use username
ACCOUNT_USER_MODEL_USERNAME_FIELD = None
# Don't require username
ACCOUNT_USERNAME_REQUIRED = False
# Still require email
ACCOUNT_EMAIL_REQUIRED = True
# Login with email (default is username)
ACCOUNT_AUTHENTICATION_METHOD = "email"


### Django Rest Auth settings
REST_AUTH_SERIALIZERS = {
    "LOGIN_SERIALIZER": "path.to.helpers.serializers.CustomLoginSerializer",
}

REST_AUTH_REGISTER_SERIALIZERS = {
    "REGISTER_SERIALIZER": "path.to.helpers.serializers.CustomRegisterSerializer",
}

```

I recommend you read up on the configuration documentation for both [Django Rest Auth](https://django-rest-auth.readthedocs.io/en/latest/configuration.html) and [Django AllAuth](https://django-allauth.readthedocs.io/en/latest/configuration.html)

After this you should be able to register and login without being prompted for username :)


**Note:** You'll encounter an error when you register and try to do the email confirmation. That is a topic for another blog post and the result for changing up the defaults.
