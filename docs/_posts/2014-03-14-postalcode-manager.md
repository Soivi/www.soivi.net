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
<p>Postalcode manager is made with PHP. It's webproject where you can add, list and delete information about finnish postalcode areas. Program uses database, sessions and cookies. I have used <a href="http://getbootstrap.com/" target="_blank">bootstrap</a> in styles.</p>
<p>Here you can try it: <a href="https://postalcode.soivi.net/" target="_blank">https://postalcode.soivi.net</a></p>
<p>You can find source codes in GitHub: <a href="https://github.com/Soivi/postalcode" target="_blank">https://github.com/Soivi/postalcode</a></p>
<h2>Screenshots</h2>
<p>[gallery columns="5" ids="1031,1029,1028,1027"]</p>
<h2>Deploy</h2>
<p>Install LAMP:<br />
<a href="https://soivi.net/2014/how-to-install-lamp/" target="_blank">https://soivi.net/2014/how-to-install-lamp/</a></p>
<p>Install git</p>
<pre>$ sudo apt-get install git</pre>
<p>Clone repository:</p>
<pre>$ git clone https://github.com/Soivi/postalcode.git</pre>
<p>Create config.php</p>
<pre>$ cd postalcode
$ nano config.php</pre>
<p>Inside config.php add these lines.</p>
<pre>
&lt;?php
        define (DSN, &quot;mysql:host=localhost;dbname=postalcodesoivi&quot;);
        define (DB_USER, &quot;user&quot;);
        define (DB_PASSWORD, &quot;password&quot;);
?&gt;
</pre>
<p>Create database</p>
<pre>
$ mysql -u root -p

CREATE DATABASE postalcodesoivi;
GRANT ALL ON postalcodesoivi.* TO user@localhost IDENTIFIED BY 'password';
exit
</pre>
<p>Add postalcode table and test data</p>
<pre>
$ mysql -u user -ppassword postalcodesoivi < create.sql
$ mysql -u user -ppassword postalcodesoivi < insert.sql
</pre>