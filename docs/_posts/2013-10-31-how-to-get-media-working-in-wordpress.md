---
layout: post
title: How to get Media working in Wordpress
date: 2013-10-31 09:44:28.000000000 +02:00
categories:
- Wordpress
tags:
- Media
- Wordpress
---

Trouples to get media / pictures working in Wordpress?  
Make uploads folder and permissions to the folder.

{% highlight shell %}
$ cd wordpress/wp-content/
$ mkdir uploads
$ chmod o+wr uploads/
{% endhighlight %}

Now you can use media in your Wordpress.
