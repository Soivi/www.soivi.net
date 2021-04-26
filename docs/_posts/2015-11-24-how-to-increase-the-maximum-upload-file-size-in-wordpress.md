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
In this post I guide how to increase maximum upload file size in Wordpress from 2mb to 64mb.

Before my way I tried to:  
- Create php.ini to WordPress root and to wp-admin  
- Modify .htaccess file  
- Modify themes function.php  
Nothing of these were working, but then I found a way what really worked. I hope this helps you too.

In my server setup I have been installed Ubuntu 12.04, other parts of LAMP and Wordpress. Here is instructions to:  
[How to install LAMP](https://soivi.net/2014/how-to-install-lamp/) and [How to install WordPress](https://soivi.net/2014/how-to-install-wordpress/)

## Finding your php configurations

As you can see my maximum upload file size is 2mb.  
![151124_MediaUploadIncrease1](/assets/2015/11/151124_MediaUploadIncrease1.png)

First you need to know where your php configurations is. Create info.php under your wp-admin.

{% highlight shell %}$ cd wordpress/wp-admin
$ nano info.php
{% endhighlight %}

In info.php add these lines. This is just normal phpinfo.

{% highlight shell %}<?php
phpinfo();
?>
{% endhighlight %}

Goto to site: yourdomain.com/wp-admin/info.php. Example I go to:

{% highlight shell %}https://soivi.net/wp-admin/info.php
{% endhighlight %}

Here you can see where php configurations are.  
![151124_MediaUploadIncrease2](/assets/2015/11/151124_MediaUploadIncrease2.png)

## Change php configurations

There are couple ways to modify php configurations. One way is to modify php.ini. Other safer way is to add in conf.d folder new ini file that overwrites php.ini configurations. This way you don't need to touch php.ini.

Go to where your "Scan this dir for additional .ini files" points and create new file upload.ini

{% highlight shell %}$ cd /etc/php5/apache2/conf.d
$ sudoedit upload.ini
{% endhighlight %}

In this upload.ini add these lines

{% highlight shell %}upload_max_filesize = 64M
post_max_size = 64M
max_execution_time = 300
{% endhighlight %}

Then restart your apache

{% highlight shell %}$ sudo service apache2 restart
{% endhighlight %}

In php info you can see the new conf.d file have been added.  
![151124_MediaUploadIncrease3](/assets/2015/11/151124_MediaUploadIncrease3.png)

Go to your media and see that maximum upload size have been increased.  
![151124_MediaUploadIncrease4](/assets/2015/11/151124_MediaUploadIncrease4.png)

Remember to delete your info.php that you created at beginning of this post.

{% highlight shell %}$ rm wordpress/wp-admin/info.php
{% endhighlight %}