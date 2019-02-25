---
layout: post
date: 2019-02-24
title: "How To Get Logging Working In GKE And GCE"
permalink: how-to-get-logging-working-in-gke-and-gce
categories:
---

I wasn't able to get GKE and GCE instances work with stackdriver when it came to logging.
Luckily, my co-worker had faced the same problem as me and had (partially) fixed it.

You simply need to configure the logger before use:

{% highlight python %}
import logging
logging.basicConfig(level=logging.INFO)

logging.info('Should appear in stackdriver')
{% endhighlight %}

The only downside to this is that **all** logs show up as `warning` instead of the desired logging level.
We didn't bother going beyond this since for us it's good enough for now.
