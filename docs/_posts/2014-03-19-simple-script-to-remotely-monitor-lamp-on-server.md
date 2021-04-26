---
layout: post
title: Simple script to remotely monitor LAMP on server
date: 2014-03-19 18:44:13.000000000 +02:00
categories:
- Linux
- Linux as a server
tags:
- Apache
- Bash
- LAMP
- Linux
- MySQL
- PHP
- Ubuntu
- Ubuntu 12.04
permalink: "/2014/simple-script-to-remotely-monitor-lamp-on-server/"
---
<p>I'm using Ubuntu 12.04 on my server and local computer<br />
If you use Windows computer for your local. Here is tutorial how to get it working<br />
<a href="http://soivi.net/2014/how-to-remotely-monitor-linux-server-from-windows/">How to remotely monitor Linux server from Windows</a><br />
<a href="http://soivi.net/wp-content/uploads/2014/03/testerError1.png"><img src="{{ site.baseurl }}/assets/2014/03/testerError1.png" alt="testerError1" width="375" height="108" class="alignright size-full wp-image-696" /></a></p>
<p>In this tutorial I'm going to create simple script that checks every 30 minutes that LAMP is running properly on my server. If something is wrong it shows notification on your local computers desktop.</p>
<h2>Install curl to local computer</h2>
<p>First install curl and test your site where you going to put your tester index.php. Anwser should be empty.</p>
<pre>
local$ sudo apt-get install curl
local$ curl tester.net
</pre>
<h2><a name="mysqlonserver" style="color:rgb(0,0,0);">Create mysql user to server</a></h2>
<p>Create user tester in MySQL</p>
<pre>
server$ mysql -u root -p

CREATE DATABASE tester;
GRANT ALL ON tester.* TO tester@localhost IDENTIFIED BY "SECRETPASSWORD";
EXIT;
</pre>
<h2><a name="phponserver" style="color:rgb(0,0,0);">Create tester php file to server</a></h2>
<p>Create folder where is index.php what checks connection to MySQL</p>
<pre>
server$ cd public_html/
server$ mkdir tester
server$ nano tester/index.php

&lt;?php
	// Create connection
	$con=mysqli_connect(&quot;localhost&quot;,&quot;tester&quot;,&quot;SECRETPASSWORD&quot;,&quot;tester&quot;);

	// Check connection
	if (!mysqli_connect_errno()) {
		echo &quot;works&quot;;
	} else {
                echo &quot;not working&quot;;
        }

?&gt; 
</pre>
<h2><a name="apacheonserver" style="color:rgb(0,0,0);">Create new site to Apache in server</a></h2>
<p>Add new site to Apache and enable it</p>
<pre>
server$ sudoedit /etc/apache2/sites-available/tester.net

&lt;VirtualHost *:80&gt;
        ServerName tester.net
        ServerAlias www.tester.net
        DocumentRoot &quot;/home/user/public_html/tester/&quot;
&lt;/VirtualHost&gt;

server$ sudo a2ensite tester.net
server$ sudo service apache2 reload
</pre>
<h2>Check that server side works using local</h2>
<p>Check that site really works. Curl should print "works"</p>
<pre>
local$ curl tester.net
works
</pre>
<h2>Create script to local</h2>
<p>Create shell script that will get tester.net site with curl and checks if site is still on.</p>
<pre>
local$ nano serverTesterScript.sh

#!/bin/bash/

URL=$(/usr/bin/curl -s tester.net)

if [ -z &quot;$URL&quot; ]
then
        /usr/bin/notify-send &#39;Something wrong with the server&#39; &#39;Answer was empty&#39;
else
        if [ &quot;$URL&quot; != &quot;works &quot; ]; then
                /usr/bin/notify-send &#39;Something wrong with the server&#39; &#39;Server doesnt work properly&#39;
        fi
fi
</pre>
<h2>Schedule script to local using crontab</h2>
<p>Then add your script to crontab so it runs script every minute.</p>
<pre>
local$ crontab -e

*/1 *   *   *   *    export DISPLAY=:0.0 && sudo -u user bash /home/user/serverTesterScript.sh
</pre>
<h2>Test everything works in local</h2>
<p>Now you can test if it works.</p>
<pre>
server$ mv public_html/tester/index.php public_html/tester/index.php2
</pre>
<p><a href="http://soivi.net/wp-content/uploads/2014/03/testerError1.png"><img src="{{ site.baseurl }}/assets/2014/03/testerError1.png" alt="testerError1" width="375" height="108" class="alignnone size-full wp-image-696" /></a></p>
<pre>
server$ mv public_html/tester/index.php2 public_html/tester/index.php
server$ sudo service apache2 stop
</pre>
<p><a href="http://soivi.net/wp-content/uploads/2014/03/testerError2.png"><img src="{{ site.baseurl }}/assets/2014/03/testerError2.png" alt="testerError2" width="378" height="110" class="alignnone size-full wp-image-697" /></a></p>
<pre>
server$ sudo service apache2 start
server$ sudo service mysql stop
</pre>
<p><a href="http://soivi.net/wp-content/uploads/2014/03/testerError3.png"><img src="{{ site.baseurl }}/assets/2014/03/testerError3.png" alt="testerError3" width="374" height="99" class="alignnone size-full wp-image-698" /></a></p>
<pre>
server$ sudo service mysql start
</pre>
<h2>Start using</h2>
<p>Now everything should work and you can edit crontab so script is runned every 30 minutes.</p>
<pre>
local$ crontab -e

*/30 *   *   *   *    export DISPLAY=:0.0 && sudo -u user bash /home/user/serverTesterScript.sh
</pre>
