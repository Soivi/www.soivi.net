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

[http://www.apachefriends.org/en/xampp-windows.html](http://www.apachefriends.org/en/xampp-windows.html)

Download ZIP file for Windows.  
Extract ZIP to you C:\ so the XAMPP folder is in C:\xampp  
Run

{% highlight shell %}$ \xampp\setup_xampp.bat{% endhighlight %}

Check the settings file

{% highlight shell %}$ \xampp\php\php.ini{% endhighlight %}

Check these three non-commented lines that they match. Date.timezone should be your timezone.

{% highlight shell %}error_reporting = E_ALL | E_STRICT
magic_quotes_gpc = Off
date.timezone = "Europe/Helsinki"{% endhighlight %}

Close Skype and IIS if you are using those.  
Run Apache and MySQL with

{% highlight shell %}$ \xampp\xampp-control.exe{% endhighlight %}

![1](/assets/2013/10/1.png)  
Go to and choose your language.

{% highlight shell %}http://localhost/{% endhighlight %}

Go to and change your MySQL password

{% highlight shell %}http://localhost/security/index.php{% endhighlight %}

![2](/assets/2013/10/2.png)

![3](/assets/2013/10/3.png)  
Stop and start Apache and MySQL with.

{% highlight shell %}$ \xampp\xampp-control.exe{% endhighlight %}

Make folder php to

{% highlight shell %}$ \xampp\htdocs\{% endhighlight %}

and create file there

{% highlight shell %}helloworld.php{% endhighlight %}

And inside the file

{% highlight shell %}<?php 
 Print "Hello, World!";
 ?>  {% endhighlight %}

Go to

{% highlight shell %}http://localhost/php/helloworld.php{% endhighlight %}

Php works.

{% highlight shell %}
Hello, World!
{% endhighlight %}

Open cmd and open MySQL with root user.

{% highlight shell %}
$ cd \xampp\mysql\bin
$ mysql.exe -u root -p
{% endhighlight %}

Create test database and user

{% highlight shell %}mysql> create database soiviPersons;
mysql> grant all on soiviPersons.* to soiviPersons@localhost identified by "SECRETPASSWORD";
mysql> exit{% endhighlight %}

Login with new user and use created database

{% highlight shell %}
$ mysql.exe -u soiviPersons -p
mysql> USE soiviPersons ;
{% endhighlight %}

Create new test table with test material

{% highlight shell %}mysql> CREATE TABLE person (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, FirstName VARCHAR(100), LastName VARCHAR(100));
mysql> INSERT INTO person (FirstName, LastName) VALUES ('Jaakko', 'Poskiparta');
mysql> INSERT INTO person (FirstName, LastName) VALUES ('Kalevi', 'Hurmeinen');
mysql> SELECT * FROM person;
mysql> exit{% endhighlight %}

Modify helloworld.php

{% highlight shell %}<!DOCTYPE html>
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
</html>{% endhighlight %}

Go to

{% highlight shell %}http://localhost/php/helloworld.php{% endhighlight %}

![4](/assets/2013/10/4.png)

Now you have tested Apache, PHP and MySQL and confirmed that they all are working.  
Then you can remove test user and test database from MySQL.

{% highlight shell %}$ mysql.exe -u root -p
mysql> DROP USER soiviPersons@localhost;
mysql> DROP DATABASE soiviPersons;
mysql> SHOW DATABASES;
mysql> exit;{% endhighlight %}

If you want use Eclipse to coding PHP you need to download PDT plugin to your Eclipse.  
Go in your Eclipse

{% highlight shell %}Help -> Install new software{% endhighlight %}

And work with you add url

{% highlight shell %}http://download.eclipse.org/tools/pdt/updates/release{% endhighlight %}

choose PDT and install it.  
![5](/assets/2013/10/5.png)
