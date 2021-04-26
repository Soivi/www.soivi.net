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
<p>This tutorial is about how to shut down a Linux computer remotely using a web page. This way you can shut down your computer from every device. You only need a device that can access the IP address of your computer and have a web browser in use. For example, with a mobile device that is connected to the same WLAN as the computer.</p>
<p><strong>WARNING: Everyone that can access your computer's IP address can shut down your computer!</strong></p>
<p>I'm using Ubuntu 16.04 and Python 2.7.12.</p>
<h2>Enable the shutdown command without your password</h2>
<p>First you need to enable the shutdown command without writing your password. Create a new file under sudoers.d folder</p>
<pre>$ sudoedit /etc/sudoers.d/shutdown
</pre>
<p>Add this next line to your new file, but remember to replace the username text with your own username</p>
<pre>username ALL=(ALL) NOPASSWD: /sbin/shutdown
</pre>
<p>Now you can run the shutdown command without the need for typing your password</p>
<pre>$ sudo shutdown -P now
</pre>
<h2>Shutdown script</h2>
<p>Create a new folder path and create a new Python script file under the cgi-bin folder</p>
<pre>$ sudo mkdir -p /var/www/cgi-bin
$ sudoedit /var/www/cgi-bin/shutdown.py
</pre>
<p>Add these next lines inside your new Python script file</p>
<pre>#! /usr/bin/python
import subprocess

print "Content-Type: text/plain;charset=utf-8"
print

print "Shutting down"

cmdCommand = "sudo shutdown -P now"
process = subprocess.Popen(cmdCommand.split(), stdout=subprocess.PIPE)
</pre>
<p>Give execution permissions to the Python script file</p>
<pre>$ sudo chmod a+x /var/www/cgi-bin/shutdown.py
</pre>
<p>Now you can test if your Python script works by running it</p>
<pre>$ python /var/www/cgi-bin/shutdown.py
</pre>
<p>After you have run the Python script, your computer should shut down. If the Python script works and the computer shuts down, next you can try to shut down your computer using a web page.</p>
<h2>Shut down a Linux computer using a web page</h2>
<p>Start a web server on port 8000</p>
<pre>$ cd /var/www/
$ python -m CGIHTTPServer 8000
</pre>
<p>The web server will start on <strong>0.0.0.0</strong>. That means every IP address that is configured into your computer should work, for example <strong>127.0.0.1</strong>. Open your web browser and go to the web page <strong>x.x.x.x:8000/cgi-bin/shutdown.py</strong> (replace x.x.x.x with your computer's IP address).</p>
<p>It should say on the web page "Shutting down" and your computer should shut down. If everything went smoothly, next you can schedule the web server to start every time your computer boots up.</p>
<h2>Schedule server to start on computer boot up</h2>
<p>Schedule the web server to start every time your computer boots up. Go to the crontab</p>
<pre>$ crontab -e
</pre>
<p>At the end of the file add this line</p>
<pre>@reboot cd /var/www/ &amp;&amp; python -m CGIHTTPServer 8000</pre>
<p>Now every time your computer starts up, you can use this next address to shut down your computer (remember to replace x.x.x.x with your own IP address)</p>
<pre>x.x.x.x:8000/cgi-bin/shutdown.py
</pre>
<p>You can use this shutdown web page on every device that can access this web page.</p>
<p>For example, you can use your mobile device and save a bookmark of the web page onto your home screen. Now you have a shortcut button on your mobile device whenever you want to shut down your computer.</p>