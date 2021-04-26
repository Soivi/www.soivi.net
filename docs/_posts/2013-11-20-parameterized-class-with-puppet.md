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
<p><strong>If you haven't seen my previous tutorials you should see them:<br />
<a href="http://soivi.net/2013/how-to-install-puppet/">How to install Puppet</a>, <a href="http://soivi.net/2013/template-hello-world-module-to-puppet/">Hello World module using template to Puppet</a>,<br />
<a href="http://soivi.net/2013/installing-apache-and-php-with-puppet-module/">Installing Apache and PHP with Puppet module</a>, <a href="http://soivi.net/2013/installing-puppet-master-and-slaves/">Installing Puppet master and slaves</a>.<strong></p>
<p>I’m using Xubuntu 12.04.03 32bit</p>
<p>This tutorial I'm creating parametrized class. Class is installing Apache2 and you can change Apaches port when running Puppet module.</p>
<p>Install Puppet, create folders and init.pp file. </p>
<pre>
$ sudo apt-get update && sudo apt-get -y install puppet
$ mkdir -p puppet/modules/apache2/manifests
$ cd puppet/
$ nano modules/apache2/manifests/init.pp
</pre>
<p>Create Apache2 class and in parameter it takes default port 80.</p>
<pre>
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
</pre>
<p>Create template folder and ports.conf.erb.</p>
<pre>
$ mkdir -p modules/apache2/templates
$ nano modules/apache2/templates/ports.conf.erb
</pre>
<p>Ports.conf.erb is copy of /etc/apache2/ports.conf, but $port ( in ports.conf.erb file &lt;%= @port %&gt; ) variable changes port what Apache is listening.</p>
<pre>
# DON&#39;T TOUCH MODIFIED BY SOIVI 20.11.2013
#
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default
# This is also true if you have upgraded from before 2.2.9-3 (i.e. from
# Debian etch). See /usr/share/doc/apache2.2-common/NEWS.Debian.gz and
# README.Debian.gz


NameVirtualHost *:80
Listen &lt;%= @port %&gt;

&lt;IfModule mod_ssl.c&gt;
    # If you add NameVirtualHost *:443 here, you will also have to change
    # the VirtualHost statement in /etc/apache2/sites-available/default-ssl
    # to &lt;VirtualHost *:443&gt;
    # Server Name Indication for SSL named virtual hosts is currently not
    # supported by MSIE on Windows XP.
    Listen 443
&lt;/IfModule&gt;

&lt;IfModule mod_gnutls.c&gt;
    Listen 443
&lt;/IfModule&gt;
</pre>
<p>Run it without parameter. Apache should get installed and should work in localhost</p>
<pre>
$ sudo puppet apply --modulepath modules/ -e 'class {"apache2":}' </pre>
<p><a href="http://soivi.net/wp-content/uploads/2013/11/ParametrizedClassWithPuppet1.png"><img src="{{ site.baseurl }}/assets/2013/11/ParametrizedClassWithPuppet1.png" alt="ParametrizedClassWithPuppet1" width="590" height="168" class="alignnone size-full wp-image-280" /></a></p>
<p>Then run Puppet and add to 8080 to port parameter.</p>
<pre>
$ sudo puppet apply --modulepath modules/ -e 'class {"apache2": port => "8080",}'
</pre>
<p>Localhost now stop working.<br />
<a href="http://soivi.net/wp-content/uploads/2013/11/ParametrizedClassWithPuppet2.png"><img src="{{ site.baseurl }}/assets/2013/11/ParametrizedClassWithPuppet2.png" alt="ParametrizedClassWithPuppet2" width="685" height="461" class="alignnone size-full wp-image-281" /></a></p>
<p>Apache should listen localhost:8080.<br />
<a href="http://soivi.net/wp-content/uploads/2013/11/ParametrizedClassWithPuppet3.png"><img src="{{ site.baseurl }}/assets/2013/11/ParametrizedClassWithPuppet3.png" alt="ParametrizedClassWithPuppet3" width="487" height="179" class="alignnone size-full wp-image-282" /></a><br />
Ok. We don't want use that port now. So let's change it back to 80.</p>
<pre>
$ sudo puppet apply --modulepath modules/ -e 'class {"apache2": port => "80",}'
</pre>
<p>Localhost:8080 doesn't work no more.<br />
<a href="http://soivi.net/wp-content/uploads/2013/11/ParametrizedClassWithPuppet4.png"><img src="{{ site.baseurl }}/assets/2013/11/ParametrizedClassWithPuppet4.png" alt="ParametrizedClassWithPuppet4" width="683" height="478" class="alignnone size-full wp-image-283" /></a></p>
<p>Now Apache listens port 80.<br />
<a href="http://soivi.net/wp-content/uploads/2013/11/ParametrizedClassWithPuppet5.png"><img src="{{ site.baseurl }}/assets/2013/11/ParametrizedClassWithPuppet5.png" alt="ParametrizedClassWithPuppet5" width="560" height="156" class="alignnone size-full wp-image-284" /></a></p>
<p>You have installed Apache2 successfully and changed Apaches port from template using parametrized class.</p>
<p>Folder tree looks like this</p>
<pre>
puppet/
└── modules
    └── apache2
        ├── manifests
        │   └── init.pp
        └── templates
            └── ports.conf.erb
</pre>
<p>This post is part of <a href="http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013">course</a></p>
