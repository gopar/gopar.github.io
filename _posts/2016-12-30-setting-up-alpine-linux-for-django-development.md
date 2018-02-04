---
layout: post
date: 2016-12-30
title: "Setting Up Alpine Linux For Django Development"
permalink: setting-up-alpine-linux-for-django-development
categories:
---

## TL;DR

Here's the [bash script](https://gist.github.com/gopar/809fedecf65239bd02fa91ad885d280d). Just change your shell to bash, install vagrant plugin and edit `Vagrantfile` settings. Then run the script.

------

If you're here, then you probably know what [Alpine](https://www.alpinelinux.org/) is. So here's a tutorial on how I setup my Django projects with it. (Projects are in Python 3.5). Also, I assume you have [vagrant](https://www.vagrantup.com/) installed.


## Installing the Plugin

I use Vagrant for VM's since it's convenient but before we can install our Alpine image we have to [install a plugin](https://github.com/maier/vagrant-alpine) that will make our lives easier. It basically "patches" some of the vagrant commands to work with Alpine. Just follow the installation instructions there.

## Setting up the VM

After that we can go ahead and create our VM. Go to any directory that you want your project to be in and run:

{% highlight bash %}
vagrant init maier/alpine-3.4-x86_64
{% endhighlight %}

This will create a file called `Vagrantfile`. Let's go ahead on edit this file and uncomment the lines that have the following:

{% highlight ruby %}
config.vm.network "forwarded_port", guest: 80, host: 8000
config.vm.network "private_network", ip: "192.168.33.10"
config.vm.synced_folder "../data", "/vagrant_data"
{% endhighlight %}

The first line is pretty self-explanatory, we access port 8000 on our local machine and then it goes, fetches and displays whatever port 80 on our VM points to. The second line is needed by the line below it. The final line is pretty important too, it's so we can share the folders/files between machines, this makes life a whole lot easier. Let's edit that line so that it fits our needs. On my directory I have another directory called `project` and I place everything there, so let's re-write this to:

{% highlight ruby %}
config.vm.synced_folder 'project/', '/home/vagrant/', type: 'nfs'
{% endhighlight %}

I just placed everything in the home directory of the VM, you can change it to wherever you'd like. You probably noticed the `type` argument as well. We need to pass this so that we can sync folders/files without any problem, I couldn't get it to work through the traditional way, so this is a work around.

## Update the Package Manager

Now that everything is setup, let's start our VM with.

{% highlight bash %}
vagrant up
{% endhighlight %}

You should start seeing text popup, just let it all go through. Eventually you should see some errors too. Don't panic, that's what supposed to happen because the VM needs to fix it's repo url. After we fix that, the next time you boot you should be asked to enter your password, this is so that we can use NFS.

Once it's all done throwing errors, run:

{% highlight bash %}
vagrant ssh
{% endhighlight %}

If it asks you for a password, it's `vagrant`. Now just update and upgrade.

{% highlight bash %}
sudo apk update
sudo apk upgrade
{% endhighlight %}


## Changing Shells

By default, Alpine sets you up with `/bin/ash` but I like `/bin/bash`. Unfortunately there is no `chsh` command so we're going to have to edit `/etc/passwd` manually.

You should see something like this:

{% highlight bash %}
vagrant:x:1000:1000:Linux User,,,:/home/vagrant:/bin/ash
{% endhighlight %}

just change the last bit to:

{% highlight bash %}
vagrant:x:1000:1000:Linux User,,,:/home/vagrant:/bin/bash
{% endhighlight %}

## Installing Python 3 and Pip

Alright, now for Python. This is where it gets easy. Just run:

{% highlight bash %}
sudo apk add python3 python3-dev gcc musl-dev jpeg-dev zlib-dev
{% endhighlight %}

You should be able to use Python 3.5 after running `python3` on the shell.
The other stuff is just dependencies that we'll need later on.

Next, is pip. These commands should do the trick:

{% highlight bash %}
wget https://bootstrap.pypa.io/get-pip.py
sudo python3 get-pip.py
rm get-pip.py
{% endhighlight %}

Ta-da! We have pip installed too. Life is wonderful huh?

## Installing Postgresql

I'll be using Postgresql for the database in this tutorial. So here's how to install it.

{% highlight bash %}
sudo apk add postgresql postgresql-client postgresql-dev
{% endhighlight %}

Next, let's start the service.

{% highlight bash %}
sudo /etc/init.d/postgresql setup
sudo /etc/init.d/postgresql start
{% endhighlight %}

If you want to start the DB on boot, do `rc-update add postgresql`.

Let's setup the the DB

{% highlight bash %}
sudo psql -U postgres -c "CREATE DATABASE tutorial";
sudo psql -U postgres -d tutorial -c "CREATE USER vagrant WITH PASSWORD 'password'";
sudo psql -U postgres -d tutorial -c "GRANT ALL PRIVILEGES ON DATABASE tutorial to vagrant";
{% endhighlight %}

## Getting virtualenvwrapper

We'll use virtualenvwrapper for our needs

{% highlight bash %}
sudo pip install virtualenvwrapper
{% endhighlight %}

Next, let's add this to our `.bashrc` file.

{% highlight bash %}
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3.5
source /usr/bin/virtualenvwrapper.sh
{% endhighlight %}

The just run `source .bashrc`. *Note*: If you're not using `bash` then sourcing the file will raise errors.


## Nginx

Finally, we can just install nginx using the package manager.

{% highlight bash %}
sudo apk add nginx
{% endhighlight %}


## Next Steps.

If you want a conf file for nginx and gunicorn working together then I recommend you [read this post by another person](https://tutos.readthedocs.io/en/latest/source/ndg.html). It's easy to follow.
