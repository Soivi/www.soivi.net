---
layout: post
title: How to ged rid of spam in Wordpress
date: 2014-03-14 14:21:40.000000000 +02:00
categories:
- Wordpress
tags:
- Anti-Spam
- G.A.S.P
- Growmap Anti Spambot
- Linux
- Plugin
- Spam
- Ubuntu
- Ubuntu 12.04
- Wordpress
- Xubuntu
- Xubuntu 12.04
permalink: "/2014/how-to-ged-rid-of-spam-in-wordpress/"
---
In this tutorial I'm going to teach how to install [Growmap Anti Spambot Plugin](https://wordpress.org/plugins/growmap-anti-spambot-plugin/) so you don't need to be worried about spam comments in your Wordpress anymore.

G.A.S.P creates a checkbox in your comment form that you need to check before you can comment. It uses client side javascript so bots should not see it. In my case it has been working and I haven't got any spam commets in a long time.

If you need more help installing plugins here is a tutorial: [How to add plugins to your WordPress manually](/2013/how-to-add-plugins-to-your-wordpress-manually/)

## Download and add plugin

Download the plugin and unzip package

{% highlight shell %}
$ wget http://downloads.wordpress.org/plugin/growmap-anti-spambot-plugin.1.5.5.zip
$ unzip growmap-anti-spambot-plugin.1.5.5.zip
{% endhighlight %}

Remove unnecessary zip and move G.A.S.P folder under plugins

{% highlight shell %}
$ rm growmap-anti-spambot-plugin.1.5.5.zip
$ mv growmap-anti-spambot-plugin/ wordpress/wp-content/plugins/
{% endhighlight %}

## Activate the plugin  

![GrowmapAntiSpambotPlugin1](/assets/2014/03/GrowmapAntiSpambotPlugin1.png)

## Now in you have installed G.A.S.P

![GrowmapAntiSpambotPlugin2](/assets/2014/03/GrowmapAntiSpambotPlugin2.png)

## Hint: how to change text

If you want change checkbox text or popUp text etc. You can change text (lines are 114-117)

{% highlight shell %}
$ nano wordpress/wp-content/plugins/growmap-anti-spambot-plugin/growmap-anti-spambot-plugin.php

114 'checkbox_alert' => __('Please check the box to confirm that you are NOT a spammer','ab_gasp'),
115 'no_checkbox_message' => __('You may have disabled javascript. Please enable javascript before leaving a comment on this site.','ab_gasp'),
116 'hidden_email_message' => __('You appear to be a spambot. Contact admin another way if you feel this message is in error','ab_gasp'),
117 'checkbox_label' => __('Confirm you are NOT a spammer','ab_gasp'),
{% endhighlight %}
