---
layout: post
title: How to install LAMP
date: 2014-01-10 15:51:18.000000000 +02:00
categories:
- Linux as a server
tags:
- Apache
- Debian
- LAMP
- Linux
- MySQL
- PHP
- Sudo
- Ubuntu
- Ubuntu 12.04
- Xubuntu
- Xubuntu 12.04
permalink: "/2014/how-to-install-lamp/"
---
I'm using Ubuntu 12.04.03 32bit  
What is [LAMP](http://en.wikipedia.org/wiki/LAMP_%28software_bundle%29)?  
This tutorial I'm going to install [Apache](http://en.wikipedia.org/wiki/Apache_HTTP_Server), [MySQL](http://en.wikipedia.org/wiki/MySQL), [PHP](http://en.wikipedia.org/wiki/PHP).

## Linux

How to install [Ubuntu](http://www.ubuntu.com/download/desktop/install-desktop-long-term-support)

## Apache

Install Apache

{% highlight shell %}
$ sudo apt-get update
$ sudo apt-get install apache2
{% endhighlight %}

Check with curl Apache is up and running (You need to install curl if you haven't install it)

{% highlight shell %}
$ curl localhost
{% endhighlight %}

Apache is working because you get default index with your localhost

{% highlight shell %}
<html><body><h1>It works!</h1>
<p>This is the default web page for this server.</p>
<p>The web server software is running but no content has been added, yet.</p>
</body></html>
{% endhighlight %}

Find out your IP address

{% highlight shell %}
$ ifconfig
{% endhighlight %}

If your using ethernet your IP is under eth0 or if your using wireless your IP is under wlan0

{% highlight shell %}
eth0 / wlan0     inet addr:1.2.3.4
{% endhighlight %}

Curl with your IP address. ( 1.2.3.4 is your IP address )

{% highlight shell %}
$ curl http://1.2.3.4/
{% endhighlight %}

Same IT WORKS should come what came in localhost.

{% highlight shell %}
<html><body><h1>It works!</h1>
...
{% endhighlight %}

Enable userdirs so computer users can use public_html folders and restart Apache

{% highlight shell %}
$ sudo a2enmod userdir
$ sudo service apache2 restart
{% endhighlight %}

Create public_html folder under your home directory and in public_html create index.html

{% highlight shell %}
$ mkdir ~/public_html
$ cd ~/public_html/
$ nano index.html
{% endhighlight %}

Add simple hello world to index.html so we can be sure userdir works

{% highlight shell %}
<!DOCTYPE HTML>
<html>
    <body>
         <p>Hello World!</p>
    </body>
</html>
{% endhighlight %}

Curl your users public_html. ( user is your users name. DON'T FORGET TO ADD SLASH END OF THE LINE! )

{% highlight shell %}
$ curl http://1.2.3.4/~user/
{% endhighlight %}

With curl there should come same code what you added in index.html

{% highlight shell %}
<!DOCTYPE HTML>
<html>
    <body>
         <p>Hello World!</p>
    </body>
</html>
{% endhighlight %}

You can test page with your firefox too.

{% highlight shell %}
$ firefox http://1.2.3.4/~user/
{% endhighlight %}

Clear default page in /var/www/index.html. ( This is for your own security. )

{% highlight shell %}
$ sudoedit /var/www/index.html
{% endhighlight %}

Erase everything in there so it's just blank page. So if you curl your IP there should come anything.

{% highlight shell %}
$ curl http://1.2.3.4/
{% endhighlight %}

Now you have installed Apache.

## PHP

Install libapache2-mod-php5 and restart Apache to get PHP working.

{% highlight shell %}
$ sudo apt-get install libapache2-mod-php5
$ sudo service apache2 restart
{% endhighlight %}

Change index.html to index.php so it's PHP file.

{% highlight shell %}
$ mv index.html index.php
$ nano index.php
{% endhighlight %}

Do simple hello world php file.

{% highlight shell %}
<!DOCTYPE html>
<html>
    <body>
        <h1>My first PHP page</h1>
        <?php
            echo "<p>Hello World!</p>\n";
        ?>
    </body>
</html>
{% endhighlight %}

To get your php working in userdirs you need to modify php5.conf

{% highlight shell %}
$ sudoedit /etc/apache2/mods-available/php5.conf
{% endhighlight %}

Comment lines so your php5.conf file looks like this.

{% highlight shell %}
<IfModule mod_php5.c>
    <FilesMatch "\.ph(p3?|tml)$">
        SetHandler application/x-httpd-php
    </FilesMatch>
    <FilesMatch "\.phps$">
        SetHandler application/x-httpd-php-source
    </FilesMatch>
    # To re-enable php in user directories comment the following lines
    # (from <IfModule ...> to </IfModule>.) Do NOT set it to On as it
    # prevents .htaccess files from disabling it.
    # <IfModule mod_userdir.c>
    #    <Directory /home/*/public_html>
    #        php_admin_value engine Off
    #    </Directory>
    # </IfModule>
</IfModule>
{% endhighlight %}

Restart Apache and curl your index.php.

{% highlight shell %}
$ sudo service apache2 restart
$ curl 1.2.3.4/~user/
{% endhighlight %}

Html code should look like this. So it works!

{% highlight shell %}
<!DOCTYPE html>
<html>
    <body>
        <h1>My first PHP page</h1>
        <p>Hello World!</p>
    </body>
</html>
{% endhighlight %}

Now PHP is working too.

## MySQL

If you want generated passwords you can use pwgen and generate 20 letter password with it. ( This is not necessary but it's helps you to get secure passwords )

{% highlight shell %}
$ sudo apt-get install pwgen
$ pwgen 20
{% endhighlight %}

Install mysql-server and php5-mysql. Restart Apache

{% highlight shell %}
$ sudo apt-get install mysql-server php5-mysql
$ sudo service apache2 restart
{% endhighlight %}

If you forget the password you can change it with the following command:

{% highlight shell %}
$ sudo dpkg-reconfigure mysql-server-5.5
{% endhighlight %}

Login to mysql as a root.

{% highlight shell %}
$ mysql -u root -p
{% endhighlight %}

Test root works. Create test database and drop it.

{% highlight shell %}
SHOW DATABASES;
CREATE DATABASE testdbsoivi;
SHOW DATABASES;
DROP DATABASE testdbsoivi;
SHOW DATABASES;
EXIT;
{% endhighlight %}

MySQL works now.

## LAMP

Now let's test whole LAMP is working together.

Login in as an root and make user person and database person.

{% highlight shell %}
$ mysql -u root -p
CREATE DATABASE person;
GRANT ALL ON person.* TO person@localhost IDENTIFIED BY 'SECRETPASSWORD';
EXIT;
{% endhighlight %}

Login as person user

{% highlight shell %}
$ mysql -u person -p
{% endhighlight %}

Use database person and create table person. In person table add two names: John Doe and Jane FooBar.

{% highlight shell %}
USE person;
CREATE TABLE person (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, FirstName VARCHAR(100), LastName VARCHAR(100));
INSERT INTO person (FirstName, LastName) VALUES ('John', 'Doe');
INSERT INTO person (FirstName, LastName) VALUES ('Jane', 'FooBar');
SELECT * FROM person;
EXIT;
{% endhighlight %}

Modify old index.php what we created earlier.

{% highlight shell %}
nano ~/public_html/index.php
{% endhighlight %}

Create simple php program that get's John Doe and Jane FooBar from MySQL and shows them in browser.

{% highlight shell %}
<!DOCTYPE html>
<html>
    <body>
        <h1>My LAMP test</h1>
        <?php
            $con=mysqli_connect("localhost","person","SECRETPASSWORD","person");
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
            echo "</table>\n";
            mysqli_close($con);
        ?>
    </body>
</html>
{% endhighlight %}

Test in your Firefox.

{% highlight shell %}
$ firefox 1.2.3.4/~user/
{% endhighlight %}

Page should look something like this.  
![LampTest](/assets/2014/01/LampTest.png)

Now you have installed whole LAMP ( Linux, Apache, MySQL, PHP ). You have also tested all those is working together.

## Cleaning test data

Of course we don't want to leave any test data to computer so we erase them.

{% highlight shell %}
$ rm ~/public_html/index.php
$ mysql -u root -p
{% endhighlight %}

Drop person database and person user.

{% highlight shell %}
SHOW DATABASES;
DROP DATABASE person;
SHOW DATABASES;
SELECT User FROM mysql.user;
DROP USER person@localhost;
SELECT User FROM mysql.user;
EXIT;
{% endhighlight %}

NEXT TUTORIAL  
[How to create virtual website](http://soivi.net/2014/how-to-create-virtual-website/)

SOURCE:  
[Samuel Kontiomaa - Installing wordpress under apache 2](http://samuelkontiomaa.com/2012/10/03/installing-wordpress-under-apache-2/)