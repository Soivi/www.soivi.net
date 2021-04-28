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
I’m using Ubuntu 12.04.03 32bit, WordPress 3.6.1 and WordPress 3.8  
Of course if you use WordPress you need to backup it at times.  
In this tutorial I show how to backup your WordPress and import that backup.

## Export

Login in your WordPress and go to Tools -> Export  
![wordpressbackup1](/assets/2014/01/wordpressbackup1.png)

"Download Export File" to your computer. Filename is something like this: name.wordpress.2014-01-17.xml.  
Now you have backupped your WordPress.

## Import

Of course we want to test if backup is really working so let's import it to empty WordPress.  
I have empty WordPress installed to my computer: [How to install WordPress](http://soivi.net/2014/how-to-install-wordpress/)  
Download [WordPress Importer](http://wordpress.org/plugins/wordpress-importer/).

{% highlight shell %}
$ wget http://downloads.wordpress.org/plugin/wordpress-importer.0.6.1.zip
{% endhighlight %}

We add plugin to WordPress manually: [How to add plugins to your WordPress manually](http://soivi.net/2013/how-to-add-plugins-to-your-wordpress-manually/)

{% highlight shell %}
$ unzip wordpress-importer.0.6.1.zip
$ mv wordpress-importer wordpress/wp-content/plugins/
$ rm wordpress-importer.0.6.1.zip
{% endhighlight %}

Before you can import your backup you need to get media working:  
[How to get Media working in WordPress](http://soivi.net/2013/how-to-get-media-working-in-wordpress/)

Go to Tools -> Import and Push "WordPress"  
![wordpressbackup2](/assets/2014/01/wordpressbackup2.png)

Browse your backup what you downloaded earlier. And Push "Upload file and Import"  
![wordpressbackup3](/assets/2014/01/wordpressbackup3.png)

Then this is just test so i don't want to create new user. So I'm using already created one "Soivitest".  
![wordpressbackup4](/assets/2014/01/wordpressbackup4.png)

Now you have imported your backup.  
![wordpressbackup5](/assets/2014/01/wordpressbackup5.png)

Make sure all blog posts and pictures are really there.  
![wordpressbackup6](/assets/2014/01/wordpressbackup6.png)

Now you have backupped your WordPress and tested your backup is really working.