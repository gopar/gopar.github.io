---
layout: post
date: 2016-04-21
title: "Using Pudb In A Virtual Environment"
permalink: using-pudb-in-a-virtual-environment
categories: python
---

If you have pudb installed globally you won't be able to run it with the Python from your virtualenv. The work around is to first install pudb through your virtualenv pip

{% highlight bash %}
pip install pudb
{% endhighlight %}

and then run:

{% highlight bash %}
python -m pudb myscript.py
{% endhighlight %}

---
Also, if you're lazy you might just add this to your `~/.bashrc`

{% highlight bash %}
alias pudb='python -m pudb'
{% endhighlight %}

So now you can simply do

{% highlight bash %}
pudb myscript.py
{% endhighlight %}
