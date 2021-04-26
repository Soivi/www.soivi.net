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
<p>I’m using Ubuntu 12.04.03 32bit and Wordpress 3.8<br />
This tutorial I'm going to install <a href="http://en.wikipedia.org/wiki/WordPress">Wordpress</a>.<br />
<a href="http://www.lennu.net/best-blogging-platform-is-wordpress/">Why to choose wordpress</a><br />
First you need to install LAMP: <a href="http://soivi.net/2014/how-to-install-lamp/">How to install LAMP</a></p>
<p>Login in to mysql as a root</p>
<pre>
$ mysql -u root -p
</pre>
<p>Create database and user what your WordPress is using.</p>
<pre>
CREATE DATABASE soiviwordpress;
GRANT ALL ON soiviwordpress.* TO soiviwordpress@localhost IDENTIFIED BY "SECRETPASSWORD";
EXIT;
</pre>
<p>Create public_html folder and download latest WordPress tar package.</p>
<pre>
$ mkdir ~/public_html/
$ cd ~/public_html/
$ wget http://wordpress.org/latest.tar.gz
</pre>
<p>Extract tar file and remove it. Then move all files and folders to public_html folder and remove wordpress folder.</p>
<pre>
$ tar -xf latest.tar.gz
$ rm latest.tar.gz
$ mv wordpress/* .
$ rmdir wordpress/
</pre>
<p>If your going to use domain name I recommend you use it when installing WordPress. That will save your time. ( I'm using virtual website. Here is tutorial how to do virtual website: <a href="http://soivi.net/2014/how-to-create-virtual-website/">How to create virtual website</a> )</p>
<p>Open firefox. ( My domain name is test.soivi where I'm going to install WordPress )</p>
<pre>
$ firefox http://test.soivi/
</pre>
<p>Push "Create Configuration File"<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/HowToInstallWordpress1.png"><img src="{{ site.baseurl }}/assets/2014/01/HowToInstallWordpress1.png" alt="HowToInstallWordpress1" width="450" height="207" class="alignnone size-full wp-image-557" /></a></p>
<p>Push "Let's go!"<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/HowToInstallWordpress2.png"><img src="{{ site.baseurl }}/assets/2014/01/HowToInstallWordpress2.png" alt="HowToInstallWordpress2" width="450" height="353" class="alignnone size-medium wp-image-558" /></a></p>
<p>Add your databases name, username and password to that user. Press "Submit"<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/HowToInstallWordpress3.png"><img src="{{ site.baseurl }}/assets/2014/01/HowToInstallWordpress3.png" alt="HowToInstallWordpress3" width="450" height="350" class="alignnone size-medium wp-image-559" /></a></p>
<p>You have to create manually wp-config.php</p>
<pre>
$ nano ~/public_html/wp-config.php
</pre>
<p>Paste whole text what WordPress is asking you to do. ( Text is something like this ).<br />
BE SURE YOU WILL COPY THE WHOLE TEXT WHAT WORDPRESS IS ASKING YOU TO COPY.</p>
<pre>
&lt;?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
...
...
...
  /** Sets up WordPress vars and included files. */
  require_once(ABSPATH . &#39;wp-settings.php&#39;);
</pre>
<p>After you have copied wp-config.php press "Run the install"<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/HowToInstallWordpress4.png"><img src="{{ site.baseurl }}/assets/2014/01/HowToInstallWordpress4.png" alt="HowToInstallWordpress4" width="450" height="318" class="alignnone size-full wp-image-560" /></a></p>
<p>Add your WordPress information. DON'T FORGET TO ALLOW SEARCH ENGINES TO INDEX YOUR SITE. This means you get more hits from search engines and people will find your blog.<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/HowToInstallWordpress5.png"><img src="{{ site.baseurl }}/assets/2014/01/HowToInstallWordpress5.png" alt="HowToInstallWordpress5" width="450" height="513" class="alignnone size-medium wp-image-561" /></a></p>
<p>You have successfully installed WordPress. Now you can Log in<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/HowToInstallWordpress6.png"><img src="{{ site.baseurl }}/assets/2014/01/HowToInstallWordpress6.png" alt="HowToInstallWordpress6" width="450" height="209" class="alignnone size-full wp-image-571" /></a></p>
<p>Add your username and password what you gave to WordPress. (NOT THE DATABASE USER AND PASSWORD)<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/HowToInstallWordpress7.png"><img src="{{ site.baseurl }}/assets/2014/01/HowToInstallWordpress7.png" alt="HowToInstallWordpress7" width="271" height="234" class="alignnone size-medium wp-image-563" /></a></p>
<p>Now you can create test post. So you can be sure WordPress really working. Remember to publish your post!<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/HowToInstallWordpress8.png"><img src="{{ site.baseurl }}/assets/2014/01/HowToInstallWordpress8.png" alt="HowToInstallWordpress8" width="450" height="276" class="alignnone size-medium wp-image-564" /></a></p>
<p>Post is published and WordPress is working<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/HowToInstallWordpress9.png"><img src="{{ site.baseurl }}/assets/2014/01/HowToInstallWordpress9.png" alt="HowToInstallWordpress9" width="450" height="300" class="alignnone size-medium wp-image-565" /></a></p>
<p>Now you have successfully installed WordPress and tested it really works.</p>