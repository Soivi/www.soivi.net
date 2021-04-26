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
I’m using Ubuntu 12.04.03 32bit

It's possible to do virtual websites so domains are seen in locally. Here is tutorial how to do that.

First you need to install Apache. Here is tutorial how you do that: [How to install LAMP](http://soivi.net/2014/how-to-install-lamp/)

Find out your ip address and add it to your hosts file

<pre>$ ifconfig
$ sudoedit /etc/hosts
</pre>

1.2.3.4 is my ip address and test.soivi and www.test.soivi are my domain names.

<pre>127.0.0.1       localhost
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

Add file test.soivi in /etc/apache2/sites-available/

<pre>$ cd /etc/apache2/sites-available/
$ sudoedit test.soivi
</pre>

This file tells where is the test.soivi's index file. This is my directory "/home/user/public_html/" where my index file is.

<pre><VirtualHost *:80>
        ServerName test.soivi
        ServerAlias www.test.soivi
        DocumentRoot "/home/user/public_html/"
</VirtualHost>
</pre>

Enable site and reload Apache

<pre>$ sudo a2ensite test.soivi
$ sudo service apache2 reload
</pre>

Now www.test.soivi and test.soivi domains works in your local computer.

<pre>$ firefox http://www.test.soivi/
$ firefox http://test.soivi/
</pre>

Now you have virtual websites in your local computer.