---
layout: post
title: How to increase the maximum upload file size in WordPress
date: 2015-11-24 19:59:35.000000000 +02:00
categories:
- Linux
- Linux as a server
tags:
- Linux
- Media
- PHP
- Server
- Ubuntu
- Ubuntu 12.04
- Wordpress
- Xubuntu
- Xubuntu 12.04
permalink: "/2015/how-to-increase-the-maximum-upload-file-size-in-wordpress/"
---
<p>In this post I guide how to increase maximum upload file size in Wordpress from 2mb to 64mb.</p>
<p>Before my way I tried to:<br />
- Create php.ini to WordPress root and to wp-admin<br />
- Modify .htaccess file<br />
- Modify themes function.php<br />
Nothing of these were working, but then I found a way what really worked. I hope this helps you too.</p>
<p>In my server setup I have been installed Ubuntu 12.04, other parts of LAMP and Wordpress. Here is instructions to:<br />
<a href="https://soivi.net/2014/how-to-install-lamp/" target="_blank">How to install LAMP</a> and <a href="https://soivi.net/2014/how-to-install-wordpress/" target="_blank">How to install WordPress</a></p>
<h2>Finding your php configurations</h2>
<p>As you can see my maximum upload file size is 2mb.<br />
<a href="http://soivi.net/wp-content/uploads/2015/11/151124_MediaUploadIncrease1.png"><img src="{{ site.baseurl }}/assets/2015/11/151124_MediaUploadIncrease1.png" alt="151124_MediaUploadIncrease1" width="1167" height="352" class="alignnone size-full wp-image-881" /></a></p>
<p>First you need to know where your php configurations is. Create info.php under your wp-admin.</p>
<pre>
$ cd wordpress/wp-admin
$ nano info.php
</pre>
<p>In info.php add these lines. This is just normal phpinfo.</p>
<pre>
&lt;?php
phpinfo();
?&gt;
</pre>
<p>Goto to site: yourdomain.com/wp-admin/info.php. Example I go to:</p>
<pre>
https://soivi.net/wp-admin/info.php
</pre>
<p>Here you can see where php configurations are.<br />
<a href="http://soivi.net/wp-content/uploads/2015/11/151124_MediaUploadIncrease2.png"><img src="{{ site.baseurl }}/assets/2015/11/151124_MediaUploadIncrease2.png" alt="151124_MediaUploadIncrease2" width="589" height="150" class="alignnone size-full wp-image-883" /></a></p>
<h2>Change php configurations</h2>
<p>There are couple ways to modify php configurations. One way is to modify php.ini. Other safer way is to add in conf.d folder new ini file that overwrites php.ini configurations. This way you don't need to touch php.ini.</p>
<p>Go to where your "Scan this dir for additional .ini files" points and create new file upload.ini</p>
<pre>
$ cd /etc/php5/apache2/conf.d
$ sudoedit upload.ini
</pre>
<p>In this upload.ini add these lines</p>
<pre>
upload_max_filesize = 64M
post_max_size = 64M
max_execution_time = 300
</pre>
<p>Then restart your apache</p>
<pre>
$ sudo service apache2 restart
</pre>
<p>In php info you can see the new conf.d file have been added.<br />
<a href="http://soivi.net/wp-content/uploads/2015/11/151124_MediaUploadIncrease3.png"><img src="{{ site.baseurl }}/assets/2015/11/151124_MediaUploadIncrease3.png" alt="151124_MediaUploadIncrease3" width="600" height="127" class="alignnone size-full wp-image-886" /></a></p>
<p>Go to your media and see that maximum upload size have been increased.<br />
<a href="http://soivi.net/wp-content/uploads/2015/11/151124_MediaUploadIncrease4.png"><img src="{{ site.baseurl }}/assets/2015/11/151124_MediaUploadIncrease4.png" alt="151124_MediaUploadIncrease4" width="1212" height="348" class="alignnone size-full wp-image-889" /></a></p>
<p>Remember to delete your info.php that you created at beginning of this post.</p>
<pre>
$ rm wordpress/wp-admin/info.php
</pre>
