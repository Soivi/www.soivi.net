---
layout: post
title: How To Create a SSL Certificate on Apache
date: 2015-11-01 17:52:33.000000000 +02:00
categories:
- Linux
- Linux as a server
tags:
- Apache
- HTTPS
- Self-Signed SSL Certificate
- Server
- SSL
- Ubuntu
- Ubuntu 12.04
permalink: "/2015/how-to-create-a-ssl-certificate-on-apache/"
---
<p>In this tutorial I guide how to add Self-Signed SSL to your Apache and to your website. This way you sites works with https. I'm using Ubuntu 12.04 LTS.</p>
<h2>Install Apache</h2>
<p>First you need to have Apache installed and site created. Here you can find instructions to that:<br />
<a href="https://soivi.net/2014/how-to-install-lamp/">https://soivi.net/2014/how-to-install-lamp/</a></p>
<h2>Enable the SSL module and create a Self-Signed SSL Certificate</h2>
<p>Enable SLL module</p>
<pre>$ sudo a2enmod ssl</pre>
<p>Create folder where you add certificates</p>
<pre>$ sudo mkdir /etc/apache2/ssl</pre>
<p>Create certificates</p>
<pre>$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt</pre>
<p>Then you need to add your information. (Important is to add in Common name your REAL address):</p>
<pre>Country Name (2 letter code) [AU]:FI
State or Province Name (full name) [Some-State]:Uusimaa
Locality Name (eg, city) []:Helsinki
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:*site.net
Email Address []:your@email.fi</pre>
<p>You can use wild cards in Common Name by adding *. So *site.net concludes hello.site.net and site.net. This way you don't need to create and add different certificates to different sites.</p>
<h2>Activate your website configuration to use Certificate</h2>
<p>Edit your site so it uses new certificate</p>
<pre>$ sudoedit /etc/apache2/sites-available/site.net

&lt;VirtualHost *:80&gt;
        ServerName site.net
        ServerAlias www.site.net
        Redirect &quot;/&quot; &quot;https://site.net/&quot;
&lt;/VirtualHost&gt;
&lt;VirtualHost *:443&gt;
        ServerName site.net
        ServerAlias www.site.net
        DocumentRoot &quot;/home/user/public_html/wordpress/&quot;
        SSLCertificateFile /etc/apache2/ssl/apache.crt
        SSLCertificateKeyFile /etc/apache2/ssl/apache.key
        SSLEngine on
&lt;/VirtualHost&gt;</pre>
<p>This is for redirection. If you try to open http://site.net you will be redirected to https://site.net</p>
<pre>&lt;VirtualHost *:80&gt;
        ServerName site.net
        ServerAlias www.site.net
        Redirect &quot;/&quot; &quot;https://site.net/&quot;
&lt;/VirtualHost&gt;</pre>
<p>This is what port is https://site.net is listening and the normal configuration what virtualhost needs.</p>
<pre>&lt;VirtualHost *:443&gt;
        ServerName site.net
        ServerAlias www.site.net
        DocumentRoot &quot;/home/user/public_html/wordpress/&quot;</pre>
<p>Here is the SLL Certificate configurations</p>
<pre>        SSLCertificateFile /etc/apache2/ssl/apache.crt
        SSLCertificateKeyFile /etc/apache2/ssl/apache.key
        SSLEngine on
&lt;/VirtualHost&gt;</pre>
<p>Restart your Apache to update new configurations.</p>
<pre>$ sudo service apache2 restart</pre>
<h2>Using many https sites</h2>
<p>If you use many https sites you get this kinda notice when you restart Apache:</p>
<pre>[warn] _default_ VirtualHost overlap on port 443, the first has precedence</pre>
<p>You can get this warning off with adding this line to ports.conf</p>
<pre>$ sudoedit /etc/apache2/ports.conf

NameVirtualHost *:443</pre>
<p>Now ports.conf looks something like this:</p>
<pre>NameVirtualHost *:80
Listen 80

&lt;IfModule mod_ssl.c&gt;
    NameVirtualHost *:443
    Listen 443
&lt;/IfModule&gt;

&lt;IfModule mod_gnutls.c&gt;
    Listen 443
&lt;/IfModule&gt;</pre>
<p>Restart service and now warning should be away.</p>
<pre>$ sudo service apache2 restart</pre>
<h2>Testing site</h2>
<p>Go to your site.net. It should redirect you to https://site.net.</p>
<p>Then you get warning like this:<br />
<a href="http://soivi.net/wp-content/uploads/2015/11/sitenetcertificate.png"><img src="{{ site.baseurl }}/assets/2015/11/sitenetcertificate.png" alt="sitenetcertificate" width="850" height="448" class="alignright size-full wp-image-839" /></a></p>
<p>This warning comes because you use Self-Signed SSL Certificate. That means browser can't confirm your certificate and there for can't trust your certificate. You get this off by purchasing trusted certificate.</p>
<p>Getting to your site using Firefox:<br />
Click I Understand the Risks - Add Exception - Confirm Security Expetion.<br />
Now your site.net should be opening and using your Self-Signed SSL Certificate.</p>
<p>On url you can see icon of the lock and that means you are using https.<br />
<a href="http://soivi.net/wp-content/uploads/2015/11/soivinetlock.png"><img src="{{ site.baseurl }}/assets/2015/11/soivinetlock.png" alt="soivinetlock" width="138" height="33" class="alignnone size-full wp-image-847" /></a></p>
<p>Source:<br />
https://www.digitalocean.com/community/tutorials/how-to-create-a-ssl-certificate-on-apache-for-ubuntu-14-04</p>