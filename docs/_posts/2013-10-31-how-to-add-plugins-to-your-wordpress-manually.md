---
layout: post
title: How to add plugins to your Wordpress manually
date: 2013-10-31 14:26:02.000000000 +02:00
categories:
- Wordpress
tags:
- Analytics
- Debian
- Google
- Google Analytics
- Linux
- Ubuntu
- Ubuntu 12.04
- Wordpress
permalink: "/2013/how-to-add-plugins-to-your-wordpress-manually/"
---
<p>If you want to add Wordpress plugins manually without using FTP here are the instructions.<br />
Example plugin i use Google Analytics.<br />
First download plugin ZIP and extract it.</p>
<pre>$ wget http://downloads.wordpress.org/plugin/google-analytics-for-wordpress.4.3.3.zip
$ unzip google-analytics-for-wordpress.4.3.3.zip</pre>
<p>Move folder what came in the ZIP file in wordpress/wp-content/plugins. And remove useless ZIP file.</p>
<pre>$ mv google-analytics-for-wordpress wordpress/wp-content/plugins/
$ rm google-analytics-for-wordpress.4.3.3.zip</pre>
<p>Then you can go to your Wordpress and choose plugins -> installed plugins and new plugin have came to the site and ready to be activated.</p>
<p><a href="http://soivi.net/wp-content/uploads/2013/10/plugin.png"><img src="{{ site.baseurl }}/assets/2013/10/plugin.png" alt="plugin" width="451" height="357" class="alignnone size-full wp-image-124" /></a></p>