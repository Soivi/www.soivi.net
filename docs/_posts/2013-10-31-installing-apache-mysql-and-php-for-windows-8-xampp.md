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
Here are instructions how to install XAMPP in Windows 8 computer.  
What is [XAMPP](http://en.wikipedia.org/wiki/XAMPP)?  
Go to

<pre>[http://www.apachefriends.org/en/xampp-windows.html](http://www.apachefriends.org/en/xampp-windows.html)</pre>

Download ZIP file for Windows.  
Extract ZIP to you C:\ so the XAMPP folder is in C:\xampp  
Run

<pre>$ \xampp\setup_xampp.bat</pre>

Check the settings file

<pre>$ \xampp\php\php.ini</pre>

Check these three non-commented lines that they match. Date.timezone should be your timezone.

<pre>error_reporting = E_ALL | E_STRICT
magic_quotes_gpc = Off
date.timezone = "Europe/Helsinki"</pre>

Close Skype and IIS if you are using those.  
Run Apache and MySQL with

<pre>$ \xampp\xampp-control.exe</pre>

[![1]({{ site.baseurl }}/assets/2013/10/1.png)](http://soivi.net/wp-content/uploads/2013/10/1.png)  
Go to and choose your language.

<pre>http://localhost/</pre>

Go to and change your MySQL password

<pre>http://localhost/security/index.php</pre>

[![2]({{ site.baseurl }}/assets/2013/10/2.png)](http://soivi.net/wp-content/uploads/2013/10/2.png)[![3]({{ site.baseurl }}/assets/2013/10/3.png)](http://soivi.net/wp-content/uploads/2013/10/3.png)  
Stop and start Apache and MySQL with.

<pre>$ \xampp\xampp-control.exe</pre>

Make folder php to

<pre>$ \xampp\htdocs\</pre>

and create file there

<pre>helloworld.php</pre>

And inside the file

<pre><?php 
 Print "Hello, World!";
 ?>  </pre>

Go to

<pre>http://localhost/php/helloworld.php</pre>

Php works.

<pre>Hello, World!</pre>

Open cmd and open MySQL with root user.

<pre>$ cd \xampp\mysql\bin
$ mysql.exe -u root -p </pre>

Create test database and user

<pre>mysql> create database soiviPersons;
mysql> grant all on soiviPersons.* to soiviPersons@localhost identified by "SECRETPASSWORD";
mysql> exit</pre>

Login with new user and use created database

<pre>$ mysql.exe -u soiviPersons -p
mysql> USE soiviPersons ; </pre>

Create new test table with test material

<pre>mysql> CREATE TABLE person (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, FirstName VARCHAR(100), LastName VARCHAR(100));
mysql> INSERT INTO person (FirstName, LastName) VALUES ('Jaakko', 'Poskiparta');
mysql> INSERT INTO person (FirstName, LastName) VALUES ('Kalevi', 'Hurmeinen');
mysql> SELECT * FROM person;
mysql> exit</pre>

Modify helloworld.php

<pre><!DOCTYPE html>
<html>
<body>
<h1>My first PHP page</h1>
<?php
$con=mysqli_connect("localhost","soiviPersons","SECRETPASSWORD","soiviPersons");
// Check connection
if (mysqli_connect_errno())
  {
  echo "Failed to connect to MySQL: " . mysqli_connect_error();
  }
$result = mysqli_query($con,"SELECT * FROM person");
echo "<table border='1'>
<tr>
<th>Firstname</th>
<th>Lastname</th>
</tr>";
while($row = mysqli_fetch_array($result))
  {
  echo "<tr>";
  echo "<td>" . $row['FirstName'] . "</td>";
  echo "<td>" . $row['LastName'] . "</td>";
  echo "</tr>";
  }
echo "</table>";
mysqli_close($con);
?>
</body>
</html></pre>

Go to

<pre>http://localhost/php/helloworld.php</pre>

[![4]({{ site.baseurl }}/assets/2013/10/4.png)](http://soivi.net/wp-content/uploads/2013/10/4.png)

Now you have tested Apache, PHP and MySQL and confirmed that they all are working.  
Then you can remove test user and test database from MySQL.

<pre>$ mysql.exe -u root -p
mysql> DROP USER soiviPersons@localhost;
mysql> DROP DATABASE soiviPersons;
mysql> SHOW DATABASES;
mysql> exit;</pre>

If you want use Eclipse to coding PHP you need to download PDT plugin to your Eclipse.  
Go in your Eclipse

<pre>Help -> Install new software</pre>

And work with you add url

<pre>http://download.eclipse.org/tools/pdt/updates/release</pre>

choose PDT and install it.  
[![5]({{ site.baseurl }}/assets/2013/10/5.png)](http://soivi.net/wp-content/uploads/2013/10/5.png)