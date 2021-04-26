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
<p><em>I'm using Ubuntu 12.04.03 32bit</em><br />
What is <a href="http://en.wikipedia.org/wiki/LAMP_%28software_bundle%29">LAMP</a>?<br />
This tutorial I'm going to install  <a href="http://en.wikipedia.org/wiki/Apache_HTTP_Server">Apache</a>, <a href="http://en.wikipedia.org/wiki/MySQL">MySQL</a>, <a href="http://en.wikipedia.org/wiki/PHP">PHP</a>.</p>
<h2>Linux</h2>
<p>How to install <a href="http://www.ubuntu.com/download/desktop/install-desktop-long-term-support">Ubuntu</a></p>
<h2>Apache</h2>
<p>Install Apache</p>
<pre>
$ sudo apt-get update
$ sudo apt-get install apache2
</pre>
<p>Check with curl Apache is up and running (You need to install curl if you haven't install it)</p>
<pre>
$ curl localhost
</pre>
<p>Apache is working because you get default index with your localhost</p>
<pre>
<em>&lt;html&gt;&lt;body&gt;&lt;h1&gt;It works!&lt;/h1&gt;
&lt;p&gt;This is the default web page for this server.&lt;/p&gt;
&lt;p&gt;The web server software is running but no content has been added, yet.&lt;/p&gt;
&lt;/body&gt;&lt;/html&gt;</em>
</pre>
<p>Find out your IP address</p>
<pre>
$ ifconfig
</pre>
<p>If your using ethernet your IP is under eth0 or if your using wireless your IP is under wlan0</p>
<pre>
<em>eth0 / wlan0     inet addr:1.2.3.4</em>
</pre>
<p>Curl with your IP address. ( 1.2.3.4 is your IP address )</p>
<pre>
$ curl http://1.2.3.4/
</pre>
<p>Same IT WORKS should come what came in localhost.</p>
<pre>
<em>&lt;html&gt;&lt;body&gt;&lt;h1&gt;It works!&lt;/h1&gt;
...</em>
</pre>
<p>Enable userdirs so computer users can use public_html folders and restart Apache</p>
<pre>
$ sudo a2enmod userdir
$ sudo service apache2 restart
</pre>
<p>Create public_html folder under your home directory and in public_html create index.html</p>
<pre>
$ mkdir ~/public_html
$ cd ~/public_html/
$ nano index.html
</pre>
<p>Add simple hello world to index.html so we can be sure userdir works</p>
<pre>
&lt;!DOCTYPE HTML&gt;
&lt;html&gt;
    &lt;body&gt;
         &lt;p&gt;Hello World!&lt;/p&gt;
    &lt;/body&gt;
&lt;/html&gt;
</pre>
<p>Curl your users public_html. ( user is your users name. DON'T FORGET TO ADD SLASH END OF THE LINE! )</p>
<pre>
$ curl http://1.2.3.4/~user/
</pre>
<p>With curl there should come same code what you added in index.html</p>
<pre>
<em>&lt;!DOCTYPE HTML&gt;
&lt;html&gt;
    &lt;body&gt;
         &lt;p&gt;Hello World!&lt;/p&gt;
    &lt;/body&gt;
&lt;/html&gt;</em>
</pre>
<p>You can test page with your firefox too.</p>
<pre>
$ firefox http://1.2.3.4/~user/
</pre>
<p>Clear default page in /var/www/index.html. ( This is for your own security. )</p>
<pre>
$ sudoedit /var/www/index.html
</pre>
<p>Erase everything in there so it's just blank page. So if you curl your IP there should come anything.</p>
<pre>
$ curl http://1.2.3.4/
</pre>
<p>Now you have installed Apache.</p>
<h2>PHP</h2>
<p>Install libapache2-mod-php5 and restart Apache to get PHP working.</p>
<pre>
$ sudo apt-get install libapache2-mod-php5
$ sudo service apache2 restart
</pre>
<p>Change index.html to index.php so it's PHP file.</p>
<pre>
$ mv index.html index.php
$ nano index.php
</pre>
<p>Do simple hello world php file.</p>
<pre>
&lt;!DOCTYPE html&gt;
&lt;html&gt;
    &lt;body&gt;
        &lt;h1&gt;My first PHP page&lt;/h1&gt;
        &lt;?php
            echo &quot;&lt;p&gt;Hello World!&lt;/p&gt;\n&quot;;
        ?&gt;
    &lt;/body&gt;
&lt;/html&gt;
</pre>
<p>To get your php working in userdirs you need to modify php5.conf</p>
<pre>
$ sudoedit /etc/apache2/mods-available/php5.conf
</pre>
<p>Comment lines so your php5.conf file looks like this.</p>
<pre>
&lt;IfModule mod_php5.c&gt;
    &lt;FilesMatch &quot;\.ph(p3?|tml)$&quot;&gt;
        SetHandler application/x-httpd-php
    &lt;/FilesMatch&gt;
    &lt;FilesMatch &quot;\.phps$&quot;&gt;
        SetHandler application/x-httpd-php-source
    &lt;/FilesMatch&gt;
    # To re-enable php in user directories comment the following lines
    # (from &lt;IfModule ...&gt; to &lt;/IfModule&gt;.) Do NOT set it to On as it
    # prevents .htaccess files from disabling it.
    # &lt;IfModule mod_userdir.c&gt;
    #    &lt;Directory /home/*/public_html&gt;
    #        php_admin_value engine Off
    #    &lt;/Directory&gt;
    # &lt;/IfModule&gt;
&lt;/IfModule&gt;
</pre>
<p>Restart Apache and curl your index.php.</p>
<pre>
$ sudo service apache2 restart
$ curl 1.2.3.4/~user/
</pre>
<p>Html code should look like this. So it works!</p>
<pre>
<em>
&lt;!DOCTYPE html&gt;
&lt;html&gt;
    &lt;body&gt;
        &lt;h1&gt;My first PHP page&lt;/h1&gt;
        &lt;p&gt;Hello World!&lt;/p&gt;
    &lt;/body&gt;
&lt;/html&gt;
</em>
</pre>
<p>Now PHP is working too.</p>
<h2>MySQL</h2>
<p>If you want generated passwords you can use pwgen and generate 20 letter password with it. ( This is not necessary but it's helps you to get secure passwords )</p>
<pre>
$ sudo apt-get install pwgen
$ pwgen 20
</pre>
<p>Install mysql-server and php5-mysql. Restart Apache</p>
<pre>
$ sudo apt-get install mysql-server php5-mysql
$ sudo service apache2 restart
</pre>
<p>If you forget the password you can change it with the following command:</p>
<pre>
$ sudo dpkg-reconfigure mysql-server-5.5
</pre>
<p>Login to mysql as a root.</p>
<pre>
$ mysql -u root -p
</pre>
<p>Test root works. Create test database and drop it.</p>
<pre>
SHOW DATABASES;
CREATE DATABASE testdbsoivi;
SHOW DATABASES;
DROP DATABASE testdbsoivi;
SHOW DATABASES;
EXIT;
</pre>
<p>MySQL works now.</p>
<h2>LAMP</h2>
<p>Now let's test whole LAMP is working together.</p>
<p>Login in as an root and make user person and database person.</p>
<pre>
$ mysql -u root -p
CREATE DATABASE person;
GRANT ALL ON person.* TO person@localhost IDENTIFIED BY 'SECRETPASSWORD';
EXIT;
</pre>
<p>Login as person user</p>
<pre>
$ mysql -u person -p
</pre>
<p>Use database person and create table person. In person table add two names: John Doe and Jane FooBar.</p>
<pre>
USE person;
CREATE TABLE person (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, FirstName VARCHAR(100), LastName VARCHAR(100));
INSERT INTO person (FirstName, LastName) VALUES ('John', 'Doe');
INSERT INTO person (FirstName, LastName) VALUES ('Jane', 'FooBar');
SELECT * FROM person;
EXIT;
</pre>
<p>Modify old index.php what we created earlier.</p>
<pre>
nano ~/public_html/index.php
</pre>
<p>Create simple php program that get's John Doe and Jane FooBar from MySQL and shows them in browser.</p>
<pre>
&lt;!DOCTYPE html&gt;
&lt;html&gt;
    &lt;body&gt;
        &lt;h1&gt;My LAMP test&lt;/h1&gt;
        &lt;?php
            $con=mysqli_connect(&quot;localhost&quot;,&quot;person&quot;,&quot;SECRETPASSWORD&quot;,&quot;person&quot;);
            // Check connection
            if (mysqli_connect_errno())
                {
                    echo &quot;Failed to connect to MySQL: &quot; . mysqli_connect_error();
                }
            $result = mysqli_query($con,&quot;SELECT * FROM person&quot;);
            echo &quot;&lt;table border=&#39;1&#39;&gt;
                &lt;tr&gt;
                    &lt;th&gt;Firstname&lt;/th&gt;
                    &lt;th&gt;Lastname&lt;/th&gt;
                &lt;/tr&gt;&quot;;
            while($row = mysqli_fetch_array($result))
                {
                    echo &quot;&lt;tr&gt;&quot;;
                    echo &quot;&lt;td&gt;&quot; . $row[&#39;FirstName&#39;] . &quot;&lt;/td&gt;&quot;;
                    echo &quot;&lt;td&gt;&quot; . $row[&#39;LastName&#39;] . &quot;&lt;/td&gt;&quot;;
                    echo &quot;&lt;/tr&gt;&quot;;
                }
            echo &quot;&lt;/table&gt;\n&quot;;
            mysqli_close($con);
        ?&gt;
    &lt;/body&gt;
&lt;/html&gt;
</pre>
<p>Test in your Firefox.</p>
<pre>
$ firefox 1.2.3.4/~user/
</pre>
<p>Page should look something like this.<br />
<a href="http://soivi.net/wp-content/uploads/2014/01/LampTest.png"><img src="{{ site.baseurl }}/assets/2014/01/LampTest.png" alt="LampTest" width="278" height="170" class="alignnone size-full wp-image-512" /></a></p>
<p>Now you have installed whole LAMP ( Linux, Apache, MySQL, PHP ). You have also tested all those is working together.</p>
<h2>Cleaning test data</h2>
<p>Of course we don't want to leave any test data to computer so we erase them.</p>
<pre>
$ rm ~/public_html/index.php
$ mysql -u root -p
</pre>
<p>Drop person database and person user.</p>
<pre>
SHOW DATABASES;
DROP DATABASE person;
SHOW DATABASES;
SELECT User FROM mysql.user;
DROP USER person@localhost;
SELECT User FROM mysql.user;
EXIT;
</pre>
<p>NEXT TUTORIAL<br />
<a href="http://soivi.net/2014/how-to-create-virtual-website/">How to create virtual website</a></p>
<p>SOURCE:<br />
<a href="http://samuelkontiomaa.com/2012/10/03/installing-wordpress-under-apache-2/">Samuel Kontiomaa - Installing wordpress under apache 2</a></p>
