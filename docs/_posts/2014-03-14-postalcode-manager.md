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
images:
  - /assets/2014/03/postalcode1.jpg
  - /assets/2014/03/postalcode2.jpg
  - /assets/2014/03/postalcode3.jpg
  - /assets/2014/03/postalcode4.jpg
---
Postalcode manager is made with PHP. It's webproject where you can add, list and delete information about finnish postalcode areas. Program uses database, sessions and cookies. I have used [bootstrap](http://getbootstrap.com/) in styles.

Here you can try it: [https://postalcode.soivi.net](https://postalcode.soivi.net/)

You can find source codes in GitHub: [https://github.com/Soivi/postalcode](https://github.com/Soivi/postalcode)

## Screenshots

{% include carousel.html images=page.images number="1" %}

## Deploy

Install LAMP:  
[https://soivi.net/2014/how-to-install-lamp/](/2014/how-to-install-lamp/)

Install git

{% highlight shell %}
$ sudo apt-get install git
{% endhighlight %}

Clone repository:

{% highlight shell %}
$ git clone https://github.com/Soivi/postalcode.git
{% endhighlight %}

Create config.php

{% highlight shell %}
$ cd postalcode
$ nano config.php
{% endhighlight %}

Inside config.php add these lines.

{% highlight shell %}
<?php
        define (DSN, "mysql:host=localhost;dbname=postalcodesoivi");
        define (DB_USER, "user");
        define (DB_PASSWORD, "password");
?>
{% endhighlight %}

Create database

{% highlight shell %}
$ mysql -u root -p

CREATE DATABASE postalcodesoivi;
GRANT ALL ON postalcodesoivi.* TO user@localhost IDENTIFIED BY 'password';
exit
{% endhighlight %}

Add postalcode table and test data

{% highlight shell %}
$ mysql -u user -ppassword postalcodesoivi < create.sql
$ mysql -u user -ppassword postalcodesoivi < insert.sql
{% endhighlight %}