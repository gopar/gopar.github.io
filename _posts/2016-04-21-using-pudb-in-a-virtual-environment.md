---
layout: post
date: 2016-04-21
title: "Using Pudb In A Virtual Environment"
permalink: using-pudb-in-a-virtual-environment
categories: python
---

If you have pudb installed globally you won't be able to run it with the Python from your virtualenv. The work around is to first install pudb through your virtualenv pip

```bash
pip install pudb
```

and then run:

```bash
python -m pudb myscript.py
```

---
Also, if you're lazy you might just add this to your `~/.bashrc`

```bash
alias pudb='python -m pudb'
```

So now you can simply do

```bash
pudb myscript.py
```
