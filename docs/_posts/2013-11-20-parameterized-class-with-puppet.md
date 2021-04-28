---
layout: post
title: Parameterized Class with Puppet
date: 2013-11-20 19:03:56.000000000 +02:00
categories:
- Linux
- Linux centralized management
tags:
- Apache
- Debian
- Linux
- Module
- Parametrized Class
- Puppet
- Template
- Ubuntu
- Ubuntu 12.04
- Xubuntu
- Xubuntu 12.04
permalink: "/2013/parameterized-class-with-puppet/"
---
**If you haven't seen my previous tutorials you should see them:  
[How to install Puppet](/2013/how-to-install-puppet/), [Hello World module using template to Puppet](/2013/template-hello-world-module-to-puppet/),  
[Installing Apache and PHP with Puppet module](/2013/installing-apache-and-php-with-puppet-module/), [Installing Puppet master and slaves](/2013/installing-puppet-master-and-slaves/).**

****

I’m using Xubuntu 12.04.03 32bit

This tutorial I'm creating parametrized class. Class is installing Apache2 and you can change Apaches port when running Puppet module.

Install Puppet, create folders and init.pp file.

{% highlight shell %}
$ sudo apt-get update && sudo apt-get -y install puppet
$ mkdir -p puppet/modules/apache2/manifests
$ cd puppet/
$ nano modules/apache2/manifests/init.pp
{% endhighlight %}

Create Apache2 class and in parameter it takes default port 80.

{% highlight shell %}
class apache2 ($port = '80') {
        package {'apache2':
                ensure => present,
        }

        service {'apache2':
                ensure => "running",
                enable => "true",
                require => Package["apache2"],
        }

        exec { "userdir":
                notify => Service["apache2"],
                command => "/usr/sbin/a2enmod userdir",
                require => Package["apache2"],
        }

        file {'/etc/apache2/ports.conf':
                content => template("apache2/ports.conf.erb"),
                require => Package["apache2"],
                notify => Service["apache2"],
        }
}
{% endhighlight %}

Create template folder and ports.conf.erb.

{% highlight shell %}
$ mkdir -p modules/apache2/templates
$ nano modules/apache2/templates/ports.conf.erb
{% endhighlight %}

Ports.conf.erb is copy of /etc/apache2/ports.conf, but $port ( in ports.conf.erb file <%= @port %> ) variable changes port what Apache is listening.

{% highlight shell %}
# DON'T TOUCH MODIFIED BY SOIVI 20.11.2013
#
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default
# This is also true if you have upgraded from before 2.2.9-3 (i.e. from
# Debian etch). See /usr/share/doc/apache2.2-common/NEWS.Debian.gz and
# README.Debian.gz

NameVirtualHost *:80
Listen <%= @port %>

<IfModule mod_ssl.c>
    # If you add NameVirtualHost *:443 here, you will also have to change
    # the VirtualHost statement in /etc/apache2/sites-available/default-ssl
    # to <VirtualHost *:443>
    # Server Name Indication for SSL named virtual hosts is currently not
    # supported by MSIE on Windows XP.
    Listen 443
</IfModule>

<IfModule mod_gnutls.c>
    Listen 443
</IfModule>
{% endhighlight %}

Run it without parameter. Apache should get installed and should work in localhost

{% highlight shell %}
$ sudo puppet apply --modulepath modules/ -e 'class {"apache2":}'
{% endhighlight %}

![ParametrizedClassWithPuppet1]({{ site.baseurl }}/assets/2013/11/ParametrizedClassWithPuppet1.png)]

Then run Puppet and add to 8080 to port parameter.

{% highlight shell %}
$ sudo puppet apply --modulepath modules/ -e 'class {"apache2": port => "8080",}'
{% endhighlight %}

Localhost now stop working.  
![ParametrizedClassWithPuppet2]({{ site.baseurl }}/assets/2013/11/ParametrizedClassWithPuppet2.png)]

Apache should listen localhost:8080.  
![ParametrizedClassWithPuppet3]({{ site.baseurl }}/assets/2013/11/ParametrizedClassWithPuppet3.png)]  
Ok. We don't want use that port now. So let's change it back to 80.

{% highlight shell %}
$ sudo puppet apply --modulepath modules/ -e 'class {"apache2": port => "80",}'
{% endhighlight %}

Localhost:8080 doesn't work no more.  
![ParametrizedClassWithPuppet4]({{ site.baseurl }}/assets/2013/11/ParametrizedClassWithPuppet4.png)]

Now Apache listens port 80.  
![ParametrizedClassWithPuppet5]({{ site.baseurl }}/assets/2013/11/ParametrizedClassWithPuppet5.png)]

You have installed Apache2 successfully and changed Apaches port from template using parametrized class.

Folder tree looks like this

{% highlight shell %}
puppet/
└── modules
    └── apache2
        ├── manifests
        │   └── init.pp
        └── templates
            └── ports.conf.erb
{% endhighlight %}

This post is part of [course](http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013)

****