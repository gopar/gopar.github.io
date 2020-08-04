---
layout: post
date: 2017-03-01
title: "Google Cloud Endpoints V2 Application Error"
permalink: google-cloud-endpoints-v2-application-error
categories: python
---

When working with [Google Cloud Endpoints Frameworks(CEF)](https://cloud.google.com/endpoints/docs/frameworks/python/about-cloud-endpoints-frameworks) there was an unexpected issue that I kept running into. The Google Cloud docs don't mention (or at least I didn't find it) about configuring your `app.yaml` file for GCE v2 usage.

For example in my `app.yaml` I had the following:

```yaml
libraries:
- name: endpoints
  version: latest
```

You would think that this would let you use the latest version of the GCE V2 but nope. I kept getting the following error:

```javascript
{"state": "APPLICATION_ERROR", "error_message": "Not Found"}
```

Turns out that you need to completely remove the `endpoints` entry from your `app.yaml` file and then it all works.
