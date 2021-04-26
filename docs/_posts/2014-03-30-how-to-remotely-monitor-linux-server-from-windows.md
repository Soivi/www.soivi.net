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
---
<p><em>I'm using Ubuntu 12.04 on my server computer and on my local computer I use Windows 8.1<br />
If you use Linux on your local computer here is tutorial how to get that working<br />
 <a href="http://soivi.net/2014/simple-script-to-remotely-monitor-lamp-on-server/">Simple script to remotely monitor LAMP on server</a></em></p>
<p><a href="http://soivi.net/wp-content/uploads/2014/03/WindowsRemoteMonitor11.png"><img src="{{ site.baseurl }}/assets/2014/03/WindowsRemoteMonitor11.png" alt="WindowsRemoteMonitor11" width="372" height="187" class="alignright size-full wp-image-748" /></a></p>
<p>This is tutorial how to monitor LAMP on Linux server remotely from Windows. I'm creating two scripts and schedule them to connect to my server in every 30minutes. If something goes wrong with the connection Windows shows notification something is wrong.</p>
<h2>Server working correctly</h2>
<p>In my previous tutorial I showed how to get your server side working correctly: <a href="http://soivi.net/2014/simple-script-to-remotely-monitor-lamp-on-server/#mysqlonserver">Simple script to remotely monitor LAMP on server</a></p>
<p>Do these steps so you can get your server side working:<br />
<a href="http://soivi.net/2014/simple-script-to-remotely-monitor-lamp-on-server/#mysqlonserver">Create mysql user to server</a><br />
<a href="http://soivi.net/2014/simple-script-to-remotely-monitor-lamp-on-server/#phponserver">Create tester php file to server</a><br />
<a href="http://soivi.net/2014/simple-script-to-remotely-monitor-lamp-on-server/#apacheonserver">Create new site to Apache in server</a></p>
<h2>Downloadin Curl and testing it</h2>
<p>When you have your server side working get Curl to your Windows computer.</p>
<p>Go get the curl.zip with ssl and unzip curl.exe where you want it. Here is link: <a href="http://curl.haxx.se/download.html">http://curl.haxx.se/download.html</a><br />
I downloaded: Win64 ia64 zip 7.33.0 binary SSL</p>
<p>Then you can run curl on you cmd. To test if it runs correctly.<br />
"C:\Directory\Curl\" is your path where you saved curl.exe</p>
<pre>
local$ C:\Directory\Curl\curl.exe tester.net
works
</pre>
<p>If you get error message<br />
<em>"The Program can't start becuase MSVCR100.dll is missing from your computer. Try reinstalling the program to fix this problem."</em><br />
You need to install Microsoft Visual C++ Redistributable Package. Depending on the version of Windows what you need to install. You can find different versions in here:<br />
<a href="http://answers.microsoft.com/en-us/windows/forum/windows_7-windows_programs/trying-to-open-computer-management-the-program/5c9d301a-2191-4edb-916e-5e4958558090">The Program can't start because MSVCR100.dll is missing</a><br />
I downloaded: Microsoft Visual C++ 2010 Redistributable Package (x64)</p>
<h2>Create .bat script</h2>
<p>Create .bat file and open notepad</p>
<pre>
local$ type NUL > serverTesterScript.bat
local$ notepad serverTesterScript.bat
</pre>
<p>Now you can create bat script what connects to your server and checks if server is working. If it's not it notifies you with popup window. It also checks if you can ping google.com so you can be sure your internet connection is on. If ping doesn't work to Google script assumes you don't have internet connection so it goes to end and doesn't do nothing.</p>
<pre>
ping www.google.com -n 1 -w 1000
if errorlevel 1 (
	goto End
)
set var=empty
for /f &quot;delims=&quot; %%a in (&#39;C:\Directory\Curl\curl.exe -s tester.net&#39;) do @set var=%%a
if not &quot;%var%&quot;==&quot;works &quot; (
		start &quot;&quot; cmd /c &quot;echo Something wrong with the server&amp;echo(&amp;pause&quot;
)
:End
</pre>
<p>EDIT:<br />
If you want bat script to work with https. Then just add -k flag to curl command like this:</p>
<pre>for /f &quot;delims=&quot; %%a in (&#39;C:\Directory\Curl\curl.exe -s -k tester.net&#39;) do @set var=%%a</pre>
<h2>Create .vbs script</h2>
<p>Then create .vbs file so you can run .bat file schedulet in background. Without everytime poping up on your screen</p>
<pre>
local$ type NUL > serverTesterScript.vbs
local$ notepad serverTesterScript.vbs
</pre>
<p>Add to "C:\Directory\" your path to serverTesterScript.bat so .vbs file will found it.</p>
<pre>
Set WshShell = CreateObject(&quot;WScript.Shell&quot;)
WshShell.Run chr(34) &amp; &quot;C:\Directory\serverTesterScript.bat&quot; &amp; Chr(34), 0
Set WshShell = Nothing
</pre>
<h2>Schedule task to run every 5min</h2>
<p>Schedule .vbs file run every 5 minutes. So we can test it.</p>
<p>Open "Task Scheduler" and create "Basic Task"</p>
<p><a href="http://soivi.net/wp-content/uploads/2014/03/WindowsRemoteMonitor1.png"><img src="{{ site.baseurl }}/assets/2014/03/WindowsRemoteMonitor1.png" alt="WindowsRemoteMonitor1" width="600" height="425" class="alignnone size-full wp-image-756" /></a></p>
<p>Create basic task that runs every day and runs your serverTesterScript.vbs script.<br />
[gallery ids="728,729,730,731,732,733"]</p>
<p>Then go your created task properties and edit task so it is repeated every 5 minutes so you can test that your script is really working<br />
[gallery ids="734,735,736"]</p>
<h2>Test script</h2>
<p>Go to your server and test these. Of course you have to wait 5 minutes every time to be sure your script really runned.</p>
<pre>
server$ mv public_html/tester/index.php public_html/tester/index.php2
</pre>
<p><a href="http://soivi.net/wp-content/uploads/2014/03/WindowsRemoteMonitor11.png"><img src="{{ site.baseurl }}/assets/2014/03/WindowsRemoteMonitor11.png" alt="WindowsRemoteMonitor11" width="473" height="238" class="alignnone size-full wp-image-748" /></a></p>
<pre>
server$ mv public_html/tester/index.php2 public_html/tester/index.php
server$ sudo service apache2 stop
</pre>
<p><a href="http://soivi.net/wp-content/uploads/2014/03/WindowsRemoteMonitor11.png"><img src="{{ site.baseurl }}/assets/2014/03/WindowsRemoteMonitor11.png" alt="WindowsRemoteMonitor11" width="473" height="238" class="alignnone size-full wp-image-748" /></a></p>
<pre>
server$ sudo service apache2 start
server$ sudo service mysql stop
</pre>
<p><a href="http://soivi.net/wp-content/uploads/2014/03/WindowsRemoteMonitor11.png"><img src="{{ site.baseurl }}/assets/2014/03/WindowsRemoteMonitor11.png" alt="WindowsRemoteMonitor11" width="473" height="238" class="alignnone size-full wp-image-748" /></a></p>
<pre>
server$ sudo service mysql start
</pre>
<h2>Start using</h2>
<p>Now you have tested your script and now you can start using it.</p>
<p>Schedule task to run every 30 minute.<br />
<a href="http://soivi.net/wp-content/uploads/2014/03/WindowsRemoteMonitor12.png"><img src="{{ site.baseurl }}/assets/2014/03/WindowsRemoteMonitor12.png" alt="WindowsRemoteMonitor12" width="560" height="33" class="alignnone size-full wp-image-758" /></a></p>
<p>Now you have script that checks every 30 minute if your server is working. If something goes wrong it notifies you.</p>
