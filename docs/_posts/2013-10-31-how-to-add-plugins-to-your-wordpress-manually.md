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
If you want to add Wordpress plugins manually without using FTP here are the instructions.  
Example plugin i use Google Analytics.  
First download plugin ZIP and extract it.

<pre>$ wget http://downloads.wordpress.org/plugin/google-analytics-for-wordpress.4.3.3.zip
$ unzip google-analytics-for-wordpress.4.3.3.zip</pre>

Move folder what came in the ZIP file in wordpress/wp-content/plugins. And remove useless ZIP file.

<pre>$ mv google-analytics-for-wordpress wordpress/wp-content/plugins/
$ rm google-analytics-for-wordpress.4.3.3.zip</pre>

Then you can go to your Wordpress and choose plugins -> installed plugins and new plugin have came to the site and ready to be activated.

[![plugin]({{ site.baseurl }}/assets/2013/10/plugin.png)](http://soivi.net/wp-content/uploads/2013/10/plugin.png)