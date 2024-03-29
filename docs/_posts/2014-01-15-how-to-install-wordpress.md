---
layout: post
title: How to install Wordpress
date: 2014-01-15 12:45:06.000000000 +02:00
categories:
- Linux as a server
- Wordpress
tags:
- Debian
- Ubuntu
- Ubuntu 12.04
- Wordpress
- Xubuntu
- Xubuntu 12.04
permalink: "/2014/how-to-install-wordpress/"
---
I’m using Ubuntu 12.04.03 32bit and Wordpress 3.8  
This tutorial I'm going to install [Wordpress](http://en.wikipedia.org/wiki/WordPress).  
[Why to choose wordpress](http://www.lennu.net/best-blogging-platform-is-wordpress/)  
First you need to install LAMP: [How to install LAMP](/2014/how-to-install-lamp/)

Login in to mysql as a root

{% highlight shell %}
$ mysql -u root -p
{% endhighlight %}

Create database and user what your WordPress is using.

{% highlight shell %}
CREATE DATABASE soiviwordpress;
GRANT ALL ON soiviwordpress.* TO soiviwordpress@localhost IDENTIFIED BY "SECRETPASSWORD";
EXIT;
{% endhighlight %}

Create public_html folder and download latest WordPress tar package.

{% highlight shell %}
$ mkdir ~/public_html/
$ cd ~/public_html/
$ wget http://wordpress.org/latest.tar.gz
{% endhighlight %}

Extract tar file and remove it. Then move all files and folders to public_html folder and remove wordpress folder.

{% highlight shell %}
$ tar -xf latest.tar.gz
$ rm latest.tar.gz
$ mv wordpress/* .
$ rmdir wordpress/
{% endhighlight %}

If your going to use domain name I recommend you use it when installing WordPress. That will save your time. ( I'm using virtual website. Here is tutorial how to do virtual website: [How to create virtual website](/2014/how-to-create-virtual-website/) )

Open firefox. ( My domain name is test.soivi where I'm going to install WordPress )

{% highlight shell %}
$ firefox http://test.soivi/
{% endhighlight %}

Push "Create Configuration File"  
![HowToInstallWordpress1](/assets/2014/01/HowToInstallWordpress1.png)

Push "Let's go!"  
![HowToInstallWordpress2](/assets/2014/01/HowToInstallWordpress2.png)

Add your databases name, username and password to that user. Press "Submit"  
![HowToInstallWordpress3](/assets/2014/01/HowToInstallWordpress3.png)

You have to create manually wp-config.php

{% highlight shell %}
$ nano ~/public_html/wp-config.php
{% endhighlight %}

Paste whole text what WordPress is asking you to do. ( Text is something like this ).  
BE SURE YOU WILL COPY THE WHOLE TEXT WHAT WORDPRESS IS ASKING YOU TO COPY.

{% highlight shell %}
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
...
...
...
  /** Sets up WordPress vars and included files. */
  require_once(ABSPATH . 'wp-settings.php');
{% endhighlight %}

After you have copied wp-config.php press "Run the install"  
![HowToInstallWordpress4](/assets/2014/01/HowToInstallWordpress4.png)

Add your WordPress information. DON'T FORGET TO ALLOW SEARCH ENGINES TO INDEX YOUR SITE. This means you get more hits from search engines and people will find your blog.  
![HowToInstallWordpress5](/assets/2014/01/HowToInstallWordpress5.png)

You have successfully installed WordPress. Now you can Log in  
![HowToInstallWordpress6](/assets/2014/01/HowToInstallWordpress6.png)

Add your username and password what you gave to WordPress. (NOT THE DATABASE USER AND PASSWORD)  
![HowToInstallWordpress7](/assets/2014/01/HowToInstallWordpress7.png)

Now you can create test post. So you can be sure WordPress really working. Remember to publish your post!  
![HowToInstallWordpress8](/assets/2014/01/HowToInstallWordpress8.png)

Post is published and WordPress is working  
![HowToInstallWordpress9](/assets/2014/01/HowToInstallWordpress9.png)

Now you have successfully installed WordPress and tested it really works.