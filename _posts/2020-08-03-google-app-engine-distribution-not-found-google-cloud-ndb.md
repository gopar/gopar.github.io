---
layout: post
title: "Google App Engine's `DistributionNotFound: google-cloud-ndb`"
date: 2020-08-03 10:18:00
categories: python
permalink: google-app-engines-distribution-not-found-google-cloud-ndb
---

When following Google's migration guide for GAE 2.7 to GAE 3.7, they mention to start using their [new ndb client `google-cloud-ndb`](https://cloud.google.com/appengine/docs/standard/python/migrate-to-python3/migrate-to-cloud-ndb). This is straighforward until we ran into the following problem:

{% highlight %}
Traceback (most recent call last):
  File "/base/alloc/tmpfs/dynamic_runtimes/python27g/python27/python27_lib/versions/1/google/appengine/runtime/wsgi.py", line 240, in Handle
    handler = _config_handle.add_wsgi_middleware(self._LoadHandler())
  File "/base/alloc/tmpfs/dynamic_runtimes/python27g/python27/python27_lib/versions/1/google/appengine/runtime/wsgi.py", line 311, in _LoadHandler
    handler, path, err = LoadObject(self._handler)
  File "/base/alloc/tmpfs/dynamic_runtimes/python27g/python27/python27_lib/versions/1/google/appengine/runtime/wsgi.py", line 85, in LoadObject
    obj = __import__(path[0])
  File "/base/alloc/tmpfs/dynamic_runtimes/python27g/python27/python27_lib/versions/third_party/setuptools-0.6c11/pkg_resources.py", line 311, in get_distribution
    if isinstance(dist,Requirement): dist = get_provider(dist)
  File "/base/alloc/tmpfs/dynamic_runtimes/python27g/python27/python27_lib/versions/third_party/setuptools-0.6c11/pkg_resources.py", line 197, in get_provider
    return working_set.find(moduleOrReq) or require(str(moduleOrReq))[0]
  File "/base/alloc/tmpfs/dynamic_runtimes/python27g/python27/python27_lib/versions/third_party/setuptools-0.6c11/pkg_resources.py", line 666, in require
    needed = self.resolve(parse_requirements(requirements))
  File "/base/alloc/tmpfs/dynamic_runtimes/python27g/python27/python27_lib/versions/third_party/setuptools-0.6c11/pkg_resources.py", line 565, in resolve
    raise DistributionNotFound(req)  # XXX put more info here
DistributionNotFound: google-cloud-ndb"
{% endhighlight %}

At first it seems like the system didn't install `google-cloud-ndb` correctly, but the **real problem** is with the version of `setuptools` that our instance was using, which as you can see from the traceback is `setuptools-0.6c11`. If you look at the Googles list of [supported 3rd party libs, that version is deprecated](https://cloud.google.com/appengine/docs/standard/python/tools/built-in-libraries-27).


The fix should be easy, update your `app.yaml` to use the latest `setuptools` (as of this writing its `36.6.0`).

{% highlight %}
...
libraries:
- name: setuptools
  version: 36.6.0 # You can also use `latest`
...
{% endhighlight %}


Unfortunately for us, the problem still persisted and we kept seeing in our logs that we were still using the old `setuptools`. After more debugging, we realized that another dependency we had in `app.yaml` was requiring the old version. My senior pointed out that if you look at `google/appengine/api/appinfo.py` inside Google's SDK, you'll see the following:

{% highlight %}
REQUIRED_LIBRARIES = {
    ('django', '1.11'): [('pytz', '2017.2')],
    ('flask', '0.12'): [('click', '6.6'), ('itsdangerous', '0.24'),
                        ('jinja2', '2.6'), ('werkzeug', '0.11.10')],
    ('jinja2', '2.6'): [('markupsafe', '0.15'), ('setuptools', '0.6c11')],
    ('jinja2', 'latest'): [('markupsafe', 'latest'), ('setuptools', 'latest')],
    ('matplotlib', '1.2.0'): [('numpy', '1.6.1')],
    ('matplotlib', 'latest'): [('numpy', 'latest')],
    ('protobuf', '3.0.0'): [('six', 'latest')],
    ('protobuf', 'latest'): [('six', 'latest')],
    ('grpcio', '1.0.0'): [('protobuf', '3.0.0'), ('enum', '0.9.23'),
                          ('futures', '3.0.5'), ('six', 'latest'),
                          ('setuptools', '36.6.0')],
    ('grpcio', 'latest'): [('protobuf', 'latest'), ('enum', 'latest'),
                           ('futures', 'latest'), ('six', 'latest'),
                           ('setuptools', 'latest')]
}
{% endhighlight %}

We were using `jinja2 2.6` which required the deprecated `setuptools`. We decided to remove `jinja2` from `app.yaml` and vendor it with the rest of the external 3rd party libraries. After that, the issue was fixed!
