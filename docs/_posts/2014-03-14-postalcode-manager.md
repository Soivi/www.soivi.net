---
layout: post
title: Postalcode manager
date: 2014-03-14 16:09:16.000000000 +02:00
categories:
- PHP
- Projects
tags:
- Bootstrap
- PHP
permalink: "/2014/postalcode-manager/"
---
Postalcode manager is made with PHP. It's webproject where you can add, list and delete information about finnish postalcode areas. Program uses database, sessions and cookies. I have used [bootstrap](http://getbootstrap.com/) in styles.

Here you can try it: [https://postalcode.soivi.net](https://postalcode.soivi.net/)

You can find source codes in GitHub: [https://github.com/Soivi/postalcode](https://github.com/Soivi/postalcode)

## Screenshots

[gallery columns="5" ids="1031,1029,1028,1027"]

## Deploy

Install LAMP:  
[https://soivi.net/2014/how-to-install-lamp/](https://soivi.net/2014/how-to-install-lamp/)

Install git

<pre>$ sudo apt-get install git</pre>

Clone repository:

<pre>$ git clone https://github.com/Soivi/postalcode.git</pre>

Create config.php

<pre>$ cd postalcode
$ nano config.php</pre>

Inside config.php add these lines.

<pre><?php
        define (DSN, "mysql:host=localhost;dbname=postalcodesoivi");
        define (DB_USER, "user");
        define (DB_PASSWORD, "password");
?>
</pre>

Create database

<pre>$ mysql -u root -p

CREATE DATABASE postalcodesoivi;
GRANT ALL ON postalcodesoivi.* TO user@localhost IDENTIFIED BY 'password';
exit
</pre>

Add postalcode table and test data

<pre>$ mysql -u user -ppassword postalcodesoivi < create.sql
$ mysql -u user -ppassword postalcodesoivi < insert.sql
</pre>