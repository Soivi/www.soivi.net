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
<p><img src="{{ site.baseurl }}/assets/2013/11/Puppet_Logo.svg_.min_.png" alt="Define Types" width="200" height="87" class="alignright size-full wp-image-1139" /></p>
<p>In this tutorial I'm creating hello_define module that uses Puppet define types. Hello_define module creates two files with different content.</p>
<p><em>If you haven't seen my previous tutorials you should see them:<br />
<a href="http://soivi.net/2013/how-to-install-puppet/">How to install Puppet</a>, <a href="http://soivi.net/2013/template-hello-world-module-to-puppet/">Hello World module using template to Puppet</a>,<br />
<a href="http://soivi.net/2013/installing-apache-and-php-with-puppet-module/">Installing Apache and PHP with Puppet module</a>, <a href="http://soivi.net/2013/installing-puppet-master-and-slaves/">Installing Puppet master and slaves</a>, <a href="http://soivi.net/2013/parametrized-class-with-puppet/">Parametrized Class with Puppet</a>.</em></p>
<p>I’m using Xubuntu 12.04.03 32bit</p>
<h2>Install and create</h2>
<p>Update apt and use it to install Puppet</p>
<pre>
$ sudo apt-get update && sudo apt-get -y install puppet
</pre>
<p>Create folders where you add your init.pp file</p>
<pre>
$ mkdir puppet/
$ cd puppet/
$ mkdir -p modules/hello_define/manifests/
</pre>
<p>Then create init.pp file and modify it</p>
<pre>
$ nano modules/hello_define/manifests/init.pp
</pre>
<p>Make hello_define class that creates two files with different content using define types. Modify your init.pp file look like this</p>
<pre>
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
</pre>
<h2>Apply Define Types</h2>
<p>Apply hello_define module with Puppet.</p>
<pre>
$ puppet apply --modulepath modules/ -e 'class {"hello_define":}'
</pre>
<p>Now hello_define module has created two files. Files are in /tmp/ folder. You can cat those files and see what are inside them</p>
<pre>
$ cat /tmp/hello_define1 
<em>Hello World. This is first define</em>

$ cat /tmp/hello_define2
<em>This is my second define. Greeting from soivi.net</em>
</pre>
<p>The folder tree looks now like this</p>
<pre>
puppet/
└── modules
    └── hello_define
        └── manifests
            └── init.pp
</pre>
<p>
Comment and let me know what you think of this guide. Did you manage to do hello_define module? Was there something incorrect?</p>
<p>
This post is part of <a href="http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013">course</a></p>
<p>I used <a href="https://puppet.com/">Puppet</a> in this blog post. Puppet is create way to centralize manage many computers at the same time. You should check it out. I have created couple beginners guides. If you want to learn more you should check them out:<br />
<a href="http://soivi.net/2013/how-to-install-puppet/">How to install Puppet</a>, <a href="http://soivi.net/2013/template-hello-world-module-to-puppet/">Hello World module using template to Puppet</a>,<br />
<a href="http://soivi.net/2013/installing-apache-and-php-with-puppet-module/">Installing Apache and PHP with Puppet module</a>, <a href="http://soivi.net/2013/installing-puppet-master-and-slaves/">Installing Puppet master and slaves</a>, <a href="http://soivi.net/2013/parametrized-class-with-puppet/">Parametrized Class with Puppet</a>.</p>
