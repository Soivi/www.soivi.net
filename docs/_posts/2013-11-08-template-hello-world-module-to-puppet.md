---
layout: post
title: Hello World module using template to Puppet
date: 2013-11-08 11:31:48.000000000 +02:00
categories:
- Linux
- Linux centralized management
tags:
- Debian
- Facter
- Linux
- Module
- Puppet
- Template
- Ubuntu
- Ubuntu 12.04
- Xubuntu
- Xubuntu 12.04
permalink: "/2013/template-hello-world-module-to-puppet/"
---
**If you haven't seen my previous tutorial you should see that:  
[How to install Puppet](http://soivi.net/2013/how-to-install-puppet/).**

I’m using Xubuntu 12.04.03 32bit  
Here is example how you can create template files to your Puppet module

Create folders and init.pp file

<pre>$ mkdir puppet/
$ cd puppet/
$ mkdir -p modules/hellotemplate/manifests/
$ nano modules/hellotemplate/manifests/init.pp

class hellotemplate {
        $testVariable = 'I am variable from init.pp'
        file { '/tmp/testModule':
                content => template("hellotemplate/testTemplate.erb"),
        }
}</pre>

Create folder to your template and template file

<pre>$ mkdir -p modules/hellotemplate/templates/
$ nano modules/hellotemplate/templates/testTemplate.erb</pre>

Create test content using facter variables

<pre>Greetings from template!
TestVariable: <%= @testVariable %>
Operating system release = <%= @operatingsystemrelease %>
<% if @operatingsystemrelease != "13.10" -%>
     Time to change to a new release?
<% else -%>
     You have the newest release!
<% end -%></pre>

Apply your hellotemplate module

<pre>$ sudo puppet apply --modulepath modules/ -e 'class {"hellotemplate":}'</pre>

Test if file was created to your computer

<pre>$ cat /tmp/testModule
Greetings from template!
TestVariable: I am variable from init.pp
Operating system release = 12.04
Time to change to a new release?</pre>

Now you have created module that uses templates!

More facter variables you can find with

<pre>$ facter
$ sudo facter 
$ sudo facter -p | less</pre>

These is what your folder/file tree should look like

<pre>puppet/
└── modules
    └── hellotemplate
        ├── manifests
        │   └── init.pp
        └── templates
            └── testTemplate.erb

4 directories, 2 files</pre>

This post is part of [course](http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013)