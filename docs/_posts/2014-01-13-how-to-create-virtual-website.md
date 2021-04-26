---
layout: post
title: How to create virtual website
date: 2014-01-13 14:27:35.000000000 +02:00
categories:
- Linux as a server
tags:
- Apache
- Debian
- Domain
- Linux
- Ubuntu
- Ubuntu 12.04
- Virtual website
- Website
- Xubuntu
- Xubuntu 12.04
permalink: "/2014/how-to-create-virtual-website/"
---
<p>I’m using Ubuntu 12.04.03 32bit</p>
<p>It's possible to do virtual websites so domains are seen in locally. Here is tutorial how to do that.</p>
<p>First you need to install Apache. Here is tutorial how you do that: <a href="http://soivi.net/2014/how-to-install-lamp/">How to install LAMP</a></p>
<p>Find out your ip address and add it to your hosts file</p>
<pre>
$ ifconfig
$ sudoedit /etc/hosts
</pre>
<p>1.2.3.4 is my ip address and test.soivi and www.test.soivi are my domain names.</p>
<pre>
127.0.0.1       localhost
127.0.1.1       yourComputerName
1.2.3.4    test.soivi
1.2.3.4    www.test.soivi

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
</pre>
<p>Add file test.soivi in /etc/apache2/sites-available/</p>
<pre>
$ cd /etc/apache2/sites-available/
$ sudoedit test.soivi
</pre>
<p>This file tells where is the test.soivi's index file. This is my directory "/home/user/public_html/" where my index file is.</p>
<pre>
&lt;VirtualHost *:80&gt;
        ServerName test.soivi
        ServerAlias www.test.soivi
        DocumentRoot &quot;/home/user/public_html/&quot;
&lt;/VirtualHost&gt;
</pre>
<p>Enable site and reload Apache</p>
<pre>
$ sudo a2ensite test.soivi
$ sudo service apache2 reload
</pre>
<p>Now www.test.soivi and test.soivi domains works in your local computer.</p>
<pre>
$ firefox http://www.test.soivi/
$ firefox http://test.soivi/
</pre>
<p>Now you have virtual websites in your local computer.</p>