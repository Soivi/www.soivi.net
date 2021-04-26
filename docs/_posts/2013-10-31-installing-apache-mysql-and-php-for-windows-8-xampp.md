---
layout: post
title: Installing Apache, MySQL and PHP for Windows 8. (XAMPP)
date: 2013-10-31 18:34:22.000000000 +02:00
categories:
- PHP
- Windows
tags:
- Apache
- Eclipse
- MySQL
- PHP
- Windows
- Windows 8
- Xampp
permalink: "/2013/installing-apache-mysql-and-php-for-windows-8-xampp/"
---
<p>Here are instructions how to install XAMPP in Windows 8 computer.<br />
What is <a href="http://en.wikipedia.org/wiki/XAMPP">XAMPP</a>?<br />
Go to</p>
<pre><a href="http://www.apachefriends.org/en/xampp-windows.html">http://www.apachefriends.org/en/xampp-windows.html</a></pre>
<p>Download ZIP file for Windows.<br />
Extract ZIP to you C:\ so the XAMPP folder is in C:\xampp<br />
Run</p>
<pre>$ \xampp\setup_xampp.bat</pre>
<p>Check the settings file</p>
<pre>$ \xampp\php\php.ini</pre>
<p>Check these three non-commented lines that they match. Date.timezone should be your timezone.</p>
<pre>error_reporting = E_ALL | E_STRICT
magic_quotes_gpc = Off
date.timezone = "Europe/Helsinki"</pre>
<p>Close Skype and IIS if you are using those.<br />
Run Apache and MySQL with</p>
<pre>$ \xampp\xampp-control.exe</pre>
<p><a href="http://soivi.net/wp-content/uploads/2013/10/1.png"><img class="alignnone size-full wp-image-115" alt="1" src="{{ site.baseurl }}/assets/2013/10/1.png" width="747" height="482" /></a><br />
Go to and choose your language.</p>
<pre>http://localhost/</pre>
<p>Go to and change your MySQL password</p>
<pre>http://localhost/security/index.php</pre>
<p><a href="http://soivi.net/wp-content/uploads/2013/10/2.png"><img class="alignnone size-full wp-image-116" alt="2" src="{{ site.baseurl }}/assets/2013/10/2.png" width="779" height="433" /></a><a href="http://soivi.net/wp-content/uploads/2013/10/3.png"><img class="alignnone size-full wp-image-117" alt="3" src="{{ site.baseurl }}/assets/2013/10/3.png" width="619" height="113" /></a><br />
Stop and start Apache and MySQL with.</p>
<pre>$ \xampp\xampp-control.exe</pre>
<p>Make folder php to</p>
<pre>$ \xampp\htdocs\</pre>
<p>and create file there</p>
<pre>helloworld.php</pre>
<p>And inside the file</p>
<pre>&lt;?php 
 Print "Hello, World!";
 ?&gt;  </pre>
<p>Go to</p>
<pre>http://localhost/php/helloworld.php</pre>
<p>Php works.</p>
<pre>Hello, World!</pre>
<p>Open cmd and open MySQL with root user.</p>
<pre>$ cd \xampp\mysql\bin
$ mysql.exe -u root -p </pre>
<p>Create test database and user</p>
<pre>
mysql> create database soiviPersons;
mysql> grant all on soiviPersons.* to soiviPersons@localhost identified by "SECRETPASSWORD";
mysql> exit</pre>
<p>Login with new user and use created database</p>
<pre>
$ mysql.exe -u soiviPersons -p
mysql> USE soiviPersons ; </pre>
<p>Create new test table with test material</p>
<pre>
mysql> CREATE TABLE person (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, FirstName VARCHAR(100), LastName VARCHAR(100));
mysql> INSERT INTO person (FirstName, LastName) VALUES ('Jaakko', 'Poskiparta');
mysql> INSERT INTO person (FirstName, LastName) VALUES ('Kalevi', 'Hurmeinen');
mysql> SELECT * FROM person;
mysql> exit</pre>
<p>Modify helloworld.php</p>
<pre>&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;body&gt;
&lt;h1&gt;My first PHP page&lt;/h1&gt;
&lt;?php
$con=mysqli_connect("localhost","soiviPersons","SECRETPASSWORD","soiviPersons");
// Check connection
if (mysqli_connect_errno())
  {
  echo "Failed to connect to MySQL: " . mysqli_connect_error();
  }
$result = mysqli_query($con,"SELECT * FROM person");
echo "&lt;table border='1'&gt;
&lt;tr&gt;
&lt;th&gt;Firstname&lt;/th&gt;
&lt;th&gt;Lastname&lt;/th&gt;
&lt;/tr&gt;";
while($row = mysqli_fetch_array($result))
  {
  echo "&lt;tr&gt;";
  echo "&lt;td&gt;" . $row['FirstName'] . "&lt;/td&gt;";
  echo "&lt;td&gt;" . $row['LastName'] . "&lt;/td&gt;";
  echo "&lt;/tr&gt;";
  }
echo "&lt;/table&gt;";
mysqli_close($con);
?&gt;
&lt;/body&gt;
&lt;/html&gt;</pre>
<p>Go to</p>
<pre>http://localhost/php/helloworld.php</pre>
<p><a href="http://soivi.net/wp-content/uploads/2013/10/4.png"><img class="alignnone size-full wp-image-118" alt="4" src="{{ site.baseurl }}/assets/2013/10/4.png" width="280" height="167" /></a></p>
<p>Now you have tested Apache, PHP and MySQL and confirmed that they all are working.<br />
Then you can remove test user and test database from MySQL.</p>
<pre>$ mysql.exe -u root -p
mysql> DROP USER soiviPersons@localhost;
mysql> DROP DATABASE soiviPersons;
mysql> SHOW DATABASES;
mysql> exit;</pre>
<p>If you want use Eclipse to coding PHP you need to download PDT plugin to your Eclipse.<br />
Go in your Eclipse</p>
<pre>Help -> Install new software</pre>
<p>And work with you add url</p>
<pre>http://download.eclipse.org/tools/pdt/updates/release</pre>
<p>choose PDT and install it.<br />
<a href="http://soivi.net/wp-content/uploads/2013/10/5.png"><img src="{{ site.baseurl }}/assets/2013/10/5.png" alt="5" width="862" height="291" class="alignnone size-full wp-image-161" /></a></p>
