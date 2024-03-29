---
layout: post
title: How to remotely monitor Linux server from Windows
date: 2014-03-30 17:11:03.000000000 +03:00
categories:
- Windows
tags:
- bat
- batch
- cURL
- Server
- vbs
- Windows
- Windows 8
permalink: "/2014/how-to-remotely-monitor-linux-server-from-windows/"
images:
  - /assets/2014/03/WindowsRemoteMonitor2.png
  - /assets/2014/03/WindowsRemoteMonitor3.png
  - /assets/2014/03/WindowsRemoteMonitor4.png
  - /assets/2014/03/WindowsRemoteMonitor5.png
  - /assets/2014/03/WindowsRemoteMonitor6.png
  - /assets/2014/03/WindowsRemoteMonitor7.png
images2:
  - /assets/2014/03/WindowsRemoteMonitor8.png
  - /assets/2014/03/WindowsRemoteMonitor9.png
  - /assets/2014/03/WindowsRemoteMonitor10.png
---
I'm using Ubuntu 12.04 on my server computer and on my local computer I use Windows 8.1  
If you use Linux on your local computer here is tutorial how to get that working  
[Simple script to remotely monitor LAMP on server](/2014/simple-script-to-remotely-monitor-lamp-on-server/)

![WindowsRemoteMonitor11](/assets/2014/03/WindowsRemoteMonitor11.png)

This is tutorial how to monitor LAMP on Linux server remotely from Windows. I'm creating two scripts and schedule them to connect to my server in every 30minutes. If something goes wrong with the connection Windows shows notification something is wrong.

## Server working correctly

In my previous tutorial I showed how to get your server side working correctly: [Simple script to remotely monitor LAMP on server](/2014/simple-script-to-remotely-monitor-lamp-on-server/#mysqlonserver)

Do these steps so you can get your server side working:  
[Create mysql user to server](/2014/simple-script-to-remotely-monitor-lamp-on-server/#mysqlonserver)  
[Create tester php file to server](/2014/simple-script-to-remotely-monitor-lamp-on-server/#phponserver)  
[Create new site to Apache in server](/2014/simple-script-to-remotely-monitor-lamp-on-server/#apacheonserver)

## Downloadin Curl and testing it

When you have your server side working get Curl to your Windows computer.

Go get the curl.zip with ssl and unzip curl.exe where you want it. Here is link: [http://curl.haxx.se/download.html](http://curl.haxx.se/download.html)  
I downloaded: Win64 ia64 zip 7.33.0 binary SSL

Then you can run curl on you cmd. To test if it runs correctly.  
"C:\Directory\Curl\" is your path where you saved curl.exe

{% highlight shell %}
local$ C:\Directory\Curl\curl.exe tester.net
works
{% endhighlight %}

If you get error message  
"The Program can't start becuase MSVCR100.dll is missing from your computer. Try reinstalling the program to fix this problem."  
You need to install Microsoft Visual C++ Redistributable Package. Depending on the version of Windows what you need to install. You can find different versions in here:  
[The Program can't start because MSVCR100.dll is missing](http://answers.microsoft.com/en-us/windows/forum/windows_7-windows_programs/trying-to-open-computer-management-the-program/5c9d301a-2191-4edb-916e-5e4958558090)  
I downloaded: Microsoft Visual C++ 2010 Redistributable Package (x64)

## Create .bat script

Create .bat file and open notepad

{% highlight shell %}
local$ type NUL > serverTesterScript.bat
local$ notepad serverTesterScript.bat
{% endhighlight %}

Now you can create bat script what connects to your server and checks if server is working. If it's not it notifies you with popup window. It also checks if you can ping google.com so you can be sure your internet connection is on. If ping doesn't work to Google script assumes you don't have internet connection so it goes to end and doesn't do nothing.

{% highlight shell %}
ping www.google.com -n 1 -w 1000
if errorlevel 1 (
	goto End
)
set var=empty
for /f "delims=" %%a in ('C:\Directory\Curl\curl.exe -s tester.net') do @set var=%%a
if not "%var%"=="works " (
		start "" cmd /c "echo Something wrong with the server&echo(&pause"
)
:End
{% endhighlight %}

EDIT:  
If you want bat script to work with https. Then just add -k flag to curl command like this:

{% highlight shell %}
for /f "delims=" %%a in ('C:\Directory\Curl\curl.exe -s -k tester.net') do @set var=%%a
{% endhighlight %}

## Create .vbs script

Then create .vbs file so you can run .bat file schedulet in background. Without everytime poping up on your screen

{% highlight shell %}
local$ type NUL > serverTesterScript.vbs
local$ notepad serverTesterScript.vbs
{% endhighlight %}

Add to "C:\Directory\" your path to serverTesterScript.bat so .vbs file will found it.

{% highlight shell %}
Set WshShell = CreateObject("WScript.Shell")
WshShell.Run chr(34) & "C:\Directory\serverTesterScript.bat" & Chr(34), 0
Set WshShell = Nothing
{% endhighlight %}

## Schedule task to run every 5min

Schedule .vbs file run every 5 minutes. So we can test it.

Open "Task Scheduler" and create "Basic Task"

![WindowsRemoteMonitor1](/assets/2014/03/WindowsRemoteMonitor1.png)

Create basic task that runs every day and runs your serverTesterScript.vbs script.  

{% include carousel.html images=page.images number="1" %}

Then go your created task properties and edit task so it is repeated every 5 minutes so you can test that your script is really working  

{% include carousel.html images=page.images2 number="2" %}

## Test script

Go to your server and test these. Of course you have to wait 5 minutes every time to be sure your script really runned.

{% highlight shell %}
server$ mv public_html/tester/index.php public_html/tester/index.php2
{% endhighlight %}

![WindowsRemoteMonitor11](/assets/2014/03/WindowsRemoteMonitor11.png)

{% highlight shell %}
server$ mv public_html/tester/index.php2 public_html/tester/index.php
server$ sudo service apache2 stop
{% endhighlight %}

![WindowsRemoteMonitor11](/assets/2014/03/WindowsRemoteMonitor11.png)

{% highlight shell %}
server$ sudo service apache2 start
server$ sudo service mysql stop
{% endhighlight %}

![WindowsRemoteMonitor11](/assets/2014/03/WindowsRemoteMonitor11.png)

{% highlight shell %}
server$ sudo service mysql start
{% endhighlight %}

## Start using

Now you have tested your script and now you can start using it.

Schedule task to run every 30 minute.  
![WindowsRemoteMonitor12](/assets/2014/03/WindowsRemoteMonitor12.png)

Now you have script that checks every 30 minute if your server is working. If something goes wrong it notifies you.