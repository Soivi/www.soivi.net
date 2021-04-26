---
layout: post
title: How to backup WordPress
date: 2014-01-17 14:10:47.000000000 +02:00
categories:
- Wordpress
tags:
- Backup
- Debian
- Linux
- Ubuntu
- Ubuntu 12.04
- Wordpress
- Xubuntu
- Xubuntu 12.04
permalink: "/2014/how-to-backup-wordpress/"
---
<p>I’m using Ubuntu 12.04.03 32bit, WordPress 3.6.1 and WordPress 3.8<br />
Of course if you use WordPress you need to backup it at times.<br />
In this tutorial I show how to backup your WordPress and import that backup.</p>
<h2>Export</h2>
<p>Login in your WordPress and go to Tools -> Export<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/wordpressbackup1.png"><img src="{{ site.baseurl }}/assets/2014/01/wordpressbackup1.png" alt="wordpressbackup1" width="1053" height="420" class="alignnone size-full wp-image-593" /></a></p>
<p>"Download Export File" to your computer. Filename is something like this: name.wordpress.2014-01-17.xml.<br />
Now you have backupped your WordPress.</p>
<h2>Import</h2>
<p>Of course we want to test if backup is really working so let's import it to empty WordPress.<br />
I have empty WordPress installed to my computer: <a href="http://soivi.net/2014/how-to-install-wordpress/">How to install WordPress</a><br />
Download <a href="http://wordpress.org/plugins/wordpress-importer/">WordPress Importer</a>.</p>
<pre>
$ wget http://downloads.wordpress.org/plugin/wordpress-importer.0.6.1.zip
</pre>
<p>We add plugin to WordPress manually: <a href="http://soivi.net/2013/how-to-add-plugins-to-your-wordpress-manually/">How to add plugins to your WordPress manually</a></p>
<pre>
$ unzip wordpress-importer.0.6.1.zip
$ mv wordpress-importer wordpress/wp-content/plugins/
$ rm wordpress-importer.0.6.1.zip
</pre>
<p>Before you can import your backup you need to get media working:<br />
<a href="http://soivi.net/2013/how-to-get-media-working-in-wordpress/">How to get Media working in WordPress</a></p>
<p>Go to Tools -> Import and Push "WordPress"<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/wordpressbackup2.png"><img src="{{ site.baseurl }}/assets/2014/01/wordpressbackup2.png" alt="wordpressbackup2" width="1228" height="474" class="alignnone size-full wp-image-594" /></a></p>
<p>Browse your backup what you downloaded earlier. And Push "Upload file and Import"<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/wordpressbackup3.png"><img src="{{ site.baseurl }}/assets/2014/01/wordpressbackup3.png" alt="wordpressbackup3" width="803" height="240" class="alignnone size-full wp-image-595" /></a></p>
<p>Then this is just test so i don't want to create new user. So I'm using already created one "Soivitest".<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/wordpressbackup4.png"><img src="{{ site.baseurl }}/assets/2014/01/wordpressbackup4.png" alt="wordpressbackup4" width="776" height="416" class="alignnone size-full wp-image-608" /></a></p>
<p>Now you have imported your backup.<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/wordpressbackup5.png"><img src="{{ site.baseurl }}/assets/2014/01/wordpressbackup5.png" alt="wordpressbackup5" width="426" height="108" class="alignnone size-full wp-image-597" /></a></p>
<p>Make sure all blog posts and pictures are really there.<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/wordpressbackup6.png"><img src="{{ site.baseurl }}/assets/2014/01/wordpressbackup6.png" alt="wordpressbackup6" width="996" height="208" class="alignnone size-full wp-image-598" /></a></p>
<p>Now you have backupped your WordPress and tested your backup is really working.</p>
