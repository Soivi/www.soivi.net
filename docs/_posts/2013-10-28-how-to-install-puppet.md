---
layout: post
title: How to install Puppet
date: 2013-10-28 14:28:20.000000000 +02:00
categories:
- Linux
- Linux centralized management
tags:
- Debian
- Gedit
- Linux
- Puppet
- Ubuntu
- Ubuntu 12.04
- Xubuntu
- Xubuntu 12.04
permalink: "/2013/how-to-install-puppet/"
---
This is step by step how you will install Puppet to your Xubuntu 12.04.03.

[What is Puppet?](http://puppetlabs.com/)

Update your packages and install Puppet

<pre>$ sudo apt-get update && sudo apt-get -y install puppet</pre>

Test if Puppet works. One line command to Puppet.

<pre>$ puppet apply -e 'file { "/tmp/helloPuppet": content => "Hello World!\n" }'
notice: /Stage[main]//File[/tmp/helloPuppet]/ensure: defined content as '{md5}8ddd8be4b179a529afa5f2ffae4b9858'
notice: Finished catalog run in 0.01 seconds</pre>

Let's make sure if the file was created.

<pre>$ cat /tmp/helloPuppet
Hello World!</pre>

Make hellotest module

<pre>$ mkdir puppet
$ cd puppet/
$ mkdir -p modules/hellotest/manifests/
$ nano modules/hellotest/manifests/init.pp

class hellotest {
    file { '/tmp/testModule':
        content => "Come visit Soivi.net!\n"
    } 
}</pre>

Apply Puppet module and test if the file is created.

<pre>$ puppet apply --modulepath modules/ -e 'class {"hellotest":}'
notice: /Stage[main]/Hellotest/File[/tmp/testModule]/ensure: defined content as '{md5}f0033e0a0e954ec5096ef8af2126fc21'
notice: Finished catalog run in 0.03 seconds
$ cat /tmp/testModule
Come visit [Soivi.net](http://soivi.net)!</pre>

Make module that installs gedit.

<pre>$ mkdir -p modules/gedit/manifests/
$ nano modules/gedit/manifests/init.pp

class gedit {
    package { "gedit":
        ensure     => present,
    }
}</pre>

Apply Puppet module that installs Gedit

<pre>$ sudo puppet apply --modulepath modules/ -e 'class {"gedit":}'

notice: /Stage[main]/Gedit/Package[gedit]/ensure: ensure changed 'purged' to 'present'
notice: Finished catalog run in 16.29 seconds</pre>

Let's test that Gedit will start

<pre>$ gedit</pre>

Now you have successfully installed Puppet and tested it.

Sources  
[http://terokarvinen.com/2013/hello-puppet-revisited-%E2%80%93-on-ubuntu-12-04-lts](http://terokarvinen.com/2013/hello-puppet-revisited-%E2%80%93-on-ubuntu-12-04-lts)  
[http://docs.puppetlabs.com/learning/ral.html](http://docs.puppetlabs.com/learning/ral.html)  
[http://docs.puppetlabs.com/learning/ordering.html](http://docs.puppetlabs.com/learning/ordering.html)

This post is part of [course](http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013)