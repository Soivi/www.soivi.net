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
I'm using Ubuntu 12.04 on my server and local computer  
If you use Windows computer for your local. Here is tutorial how to get it working  
[How to remotely monitor Linux server from Windows](/2014/how-to-remotely-monitor-linux-server-from-windows/)  
![testerError1](/assets/2014/03/testerError1.png)

In this tutorial I'm going to create simple script that checks every 30 minutes that LAMP is running properly on my server. If something is wrong it shows notification on your local computers desktop.

## Install curl to local computer

First install curl and test your site where you going to put your tester index.php. Anwser should be empty.

{% highlight shell %}
local$ sudo apt-get install curl
local$ curl tester.net
{% endhighlight %}

## <a name="mysqlonserver" style="color:rgb(0,0,0);">Create mysql user to server</a>

Create user tester in MySQL

{% highlight shell %}
server$ mysql -u root -p

CREATE DATABASE tester;
GRANT ALL ON tester.* TO tester@localhost IDENTIFIED BY "SECRETPASSWORD";
EXIT;
{% endhighlight %}

## <a name="phponserver" style="color:rgb(0,0,0);">Create tester php file to server</a>

Create folder where is index.php what checks connection to MySQL

{% highlight shell %}
server$ cd public_html/
server$ mkdir tester
server$ nano tester/index.php

<?php
	// Create connection
	$con=mysqli_connect("localhost","tester","SECRETPASSWORD","tester");

	// Check connection
	if (!mysqli_connect_errno()) {
		echo "works";
	} else {
                echo "not working";
        }

?> 
{% endhighlight %}

## <a name="apacheonserver" style="color:rgb(0,0,0);">Create new site to Apache in server</a>

Add new site to Apache and enable it

{% highlight shell %}
server$ sudoedit /etc/apache2/sites-available/tester.net

<VirtualHost *:80>
        ServerName tester.net
        ServerAlias www.tester.net
        DocumentRoot "/home/user/public_html/tester/"
</VirtualHost>

server$ sudo a2ensite tester.net
server$ sudo service apache2 reload
{% endhighlight %}

## Check that server side works using local

Check that site really works. Curl should print "works"

{% highlight shell %}
local$ curl tester.net
works
{% endhighlight %}

## Create script to local

Create shell script that will get tester.net site with curl and checks if site is still on.

{% highlight shell %}
local$ nano serverTesterScript.sh

#!/bin/bash/

URL=$(/usr/bin/curl -s tester.net)

if [ -z "$URL" ]
then
        /usr/bin/notify-send 'Something wrong with the server' 'Answer was empty'
else
        if [ "$URL" != "works " ]; then
                /usr/bin/notify-send 'Something wrong with the server' 'Server doesnt work properly'
        fi
fi
{% endhighlight %}

## Schedule script to local using crontab

Then add your script to crontab so it runs script every minute.

{% highlight shell %}
local$ crontab -e

*/1 *   *   *   *    export DISPLAY=:0.0 && sudo -u user bash /home/user/serverTesterScript.sh
{% endhighlight %}

## Test everything works in local

Now you can test if it works.

{% highlight shell %}
server$ mv public_html/tester/index.php public_html/tester/index.php2
{% endhighlight %}

![testerError1](/assets/2014/03/testerError1.png)

{% highlight shell %}
server$ mv public_html/tester/index.php2 public_html/tester/index.php
server$ sudo service apache2 stop
{% endhighlight %}

![testerError2](/assets/2014/03/testerError2.png)

{% highlight shell %}
server$ sudo service apache2 start
server$ sudo service mysql stop
{% endhighlight %}

![testerError3](/assets/2014/03/testerError3.png)

{% highlight shell %}
server$ sudo service mysql start
{% endhighlight %}

## Start using

Now everything should work and you can edit crontab so script is runned every 30 minutes.

{% highlight shell %}
local$ crontab -e

*/30 *   *   *   *    export DISPLAY=:0.0 && sudo -u user bash /home/user/serverTesterScript.sh
{% endhighlight %}