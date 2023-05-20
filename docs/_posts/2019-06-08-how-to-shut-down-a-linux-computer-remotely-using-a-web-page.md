---
layout: post
title: How to shut down a Linux computer remotely using a web page
date: 2019-06-08 21:54:17.000000000 +03:00
categories:
- Linux
- Linux as a server
tags:
- Android
- iOS
- iPad
- Linux
- Mobile
- Pad
- Phone
- Python
- Script
- Server
- Shutdown
- Tablet
- Ubuntu
- Web page
- Website
permalink: "/2019/how-to-shut-down-a-linux-computer-remotely-using-a-web-page/"
---
This tutorial is about how to shut down a Linux computer remotely using a web page. This way you can shut down your computer from every device. You only need a device that can access the IP address of your computer and have a web browser in use. For example, with a mobile device that is connected to the same WLAN as the computer.

**WARNING: Everyone that can access your computer's IP address can shut down your computer!**

I'm using Ubuntu 16.04 and Python 2.7.12.

## Enable the shutdown command without your password

First you need to enable the shutdown command without writing your password. Create a new file under sudoers.d folder

{% highlight shell %}
$ sudoedit /etc/sudoers.d/shutdown
{% endhighlight %}

Add this next line to your new file, but remember to replace the username text with your own username

{% highlight shell %}
username ALL=(ALL) NOPASSWD: /sbin/shutdown
{% endhighlight %}

Now you can run the shutdown command without the need for typing your password

{% highlight shell %}
$ sudo shutdown -P now
{% endhighlight %}

## Shutdown script

Create a new folder path and create a new Python script file under the cgi-bin folder

{% highlight shell %}
$ sudo mkdir -p /var/www/cgi-bin
$ sudoedit /var/www/cgi-bin/shutdown.py
{% endhighlight %}

Add these next lines inside your new Python script file

{% highlight shell %}
#! /usr/bin/python
import subprocess

print "Content-Type: text/plain;charset=utf-8"
print

print "Shutting down"

cmdCommand = "sudo shutdown -P now"
process = subprocess.Popen(cmdCommand.split(), stdout=subprocess.PIPE)
{% endhighlight %}

Give execution permissions to the Python script file

{% highlight shell %}
$ sudo chmod a+x /var/www/cgi-bin/shutdown.py
{% endhighlight %}

Now you can test if your Python script works by running it

{% highlight shell %}
$ python /var/www/cgi-bin/shutdown.py
{% endhighlight %}

After you have run the Python script, your computer should shut down. If the Python script works and the computer shuts down, next you can try to shut down your computer using a web page.

## Shut down a Linux computer using a web page

Start a web server on port 8000

{% highlight shell %}
$ cd /var/www/
$ python -m CGIHTTPServer 8000
{% endhighlight %}

The web server will start on **0.0.0.0**. That means every IP address that is configured into your computer should work, for example **127.0.0.1**. Open your web browser and go to the web page **x.x.x.x:8000/cgi-bin/shutdown.py** (replace x.x.x.x with your computer's IP address).

It should say on the web page "Shutting down" and your computer should shut down. If everything went smoothly, next you can schedule the web server to start every time your computer boots up.

## Schedule server to start on computer boot up

Schedule the web server to start every time your computer boots up. Go to the crontab

{% highlight shell %}
$ crontab -e
{% endhighlight %}

At the end of the file add this line

{% highlight shell %}
@reboot cd /var/www/ && python -m CGIHTTPServer 8000
{% endhighlight %}

Now every time your computer starts up, you can use this next address to shut down your computer (remember to replace x.x.x.x with your own IP address)

{% highlight shell %}
x.x.x.x:8000/cgi-bin/shutdown.py
{% endhighlight %}

You can use this shutdown web page on every device that can access this web page.

For example, you can use your mobile device and save a bookmark of the web page onto your home screen. Now you have a shortcut button on your mobile device whenever you want to shut down your computer.