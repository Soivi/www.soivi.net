---
layout: post
title: Define Types example in Puppet
date: 2013-11-22 13:59:32.000000000 +02:00
categories:
- Linux
- Linux centralized management
tags:
- Debian
- Define types
- Linux
- Module
- Puppet
- Ubuntu
- Ubuntu 12.04
- Xubuntu
- Xubuntu 12.04
permalink: "/2013/define-types-example-in-puppet/"
---
![Define Types]({{ site.baseurl }}/assets/2013/11/Puppet_Logo.svg_.min_.png)

In this tutorial I'm creating hello_define module that uses Puppet define types. Hello_define module creates two files with different content.

**If you haven't seen my previous tutorials you should see them:  
[How to install Puppet](http://soivi.net/2013/how-to-install-puppet/), [Hello World module using template to Puppet](http://soivi.net/2013/template-hello-world-module-to-puppet/),  
[Installing Apache and PHP with Puppet module](http://soivi.net/2013/installing-apache-and-php-with-puppet-module/), [Installing Puppet master and slaves](http://soivi.net/2013/installing-puppet-master-and-slaves/), [Parametrized Class with Puppet](http://soivi.net/2013/parametrized-class-with-puppet/).**

I’m using Xubuntu 12.04.03 32bit

## Install and create

Update apt and use it to install Puppet

{% highlight shell %}
$ sudo apt-get update && sudo apt-get -y install puppet
{% endhighlight %}

Create folders where you add your init.pp file

{% highlight shell %}
$ mkdir puppet/
$ cd puppet/
$ mkdir -p modules/hello_define/manifests/
{% endhighlight %}

Then create init.pp file and modify it

{% highlight shell %}
$ nano modules/hello_define/manifests/init.pp
{% endhighlight %}

Make hello_define class that creates two files with different content using define types. Modify your init.pp file look like this

{% highlight shell %}
class hello_define {
    define hello_define ($content_variable) {
      file {"$title":
        ensure  => file,
        content => $content_variable,
      }
    }

    hello_define {'/tmp/hello_define1':
      content_variable => "Hello World. This is first define\n",
    }

    hello_define {'/tmp/hello_define2':
      content_variable => "This is my second define. Greeting from soivi.net\n",
    }
}
{% endhighlight %}

## Apply Define Types

Apply hello_define module with Puppet.

{% highlight shell %}
$ puppet apply --modulepath modules/ -e 'class {"hello_define":}'
{% endhighlight %}

Now hello_define module has created two files. Files are in /tmp/ folder. You can cat those files and see what are inside them

{% highlight shell %}
$ cat /tmp/hello_define1 
Hello World. This is first define

$ cat /tmp/hello_define2
This is my second define. Greeting from soivi.net
{% endhighlight %}

The folder tree looks now like this

{% highlight shell %}
puppet/
└── modules
    └── hello_define
        └── manifests
            └── init.pp
{% endhighlight %}

Comment and let me know what you think of this guide. Did you manage to do hello_define module? Was there something incorrect?

This post is part of [course](http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013)

I used [Puppet](https://puppet.com/) in this blog post. Puppet is create way to centralize manage many computers at the same time. You should check it out. I have created couple beginners guides. If you want to learn more you should check them out:  
[How to install Puppet](http://soivi.net/2013/how-to-install-puppet/), [Hello World module using template to Puppet](http://soivi.net/2013/template-hello-world-module-to-puppet/),  
[Installing Apache and PHP with Puppet module](http://soivi.net/2013/installing-apache-and-php-with-puppet-module/), [Installing Puppet master and slaves](http://soivi.net/2013/installing-puppet-master-and-slaves/), [Parametrized Class with Puppet](http://soivi.net/2013/parametrized-class-with-puppet/).