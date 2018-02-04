---
layout: post
date: 2017-06-17
title: "How To Add Custom Headers To Google Cloud Endpoints"
permalink: how-to-add-custom-headers-to-google-cloud-endpoints
categories:
---

If you want to add custom response headers to GCE (Google Cloud Endpoints),
you're going to have to do some monkey patching.

{% highlight python %}
import endpoints.util as util

# Note: If someone imports send_wsgi_response before here, the function will NOT be decorated
# and the original function will be used until this bit runs
def add_headers(wsgi_func):
    def wrapper(status, headers, content, start_response, cors_handler=None):
        headers.append(('Some-Header', 'some-value'))
        return wsgi_func(status, headers, content, start_response, cors_handler)
    return wrapper

util.send_wsgi_response = add_headers(util.send_wsgi_response)
{% endhighlight %}

I put this bit in my `__init__.py` that takes care of loading up the service, since I want to make sure this runs first. This is specific example for my needs, so customize as you see fit.
