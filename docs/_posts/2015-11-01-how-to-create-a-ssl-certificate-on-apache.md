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
In this tutorial I guide how to add Self-Signed SSL to your Apache and to your website. This way you sites works with https. I'm using Ubuntu 12.04 LTS.

## Install Apache

First you need to have Apache installed and site created. Here you can find instructions to that:  
[https://soivi.net/2014/how-to-install-lamp/](https://soivi.net/2014/how-to-install-lamp/)

## Enable the SSL module and create a Self-Signed SSL Certificate

Enable SLL module

<pre>$ sudo a2enmod ssl</pre>

Create folder where you add certificates

<pre>$ sudo mkdir /etc/apache2/ssl</pre>

Create certificates

<pre>$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt</pre>

Then you need to add your information. (Important is to add in Common name your REAL address):

<pre>Country Name (2 letter code) [AU]:FI
State or Province Name (full name) [Some-State]:Uusimaa
Locality Name (eg, city) []:Helsinki
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:*site.net
Email Address []:your@email.fi</pre>

You can use wild cards in Common Name by adding *. So *site.net concludes hello.site.net and site.net. This way you don't need to create and add different certificates to different sites.

## Activate your website configuration to use Certificate

Edit your site so it uses new certificate

<pre>$ sudoedit /etc/apache2/sites-available/site.net

<VirtualHost *:80>
        ServerName site.net
        ServerAlias www.site.net
        Redirect "/" "https://site.net/"
</VirtualHost>
<VirtualHost *:443>
        ServerName site.net
        ServerAlias www.site.net
        DocumentRoot "/home/user/public_html/wordpress/"
        SSLCertificateFile /etc/apache2/ssl/apache.crt
        SSLCertificateKeyFile /etc/apache2/ssl/apache.key
        SSLEngine on
</VirtualHost></pre>

This is for redirection. If you try to open http://site.net you will be redirected to https://site.net

<pre><VirtualHost *:80>
        ServerName site.net
        ServerAlias www.site.net
        Redirect "/" "https://site.net/"
</VirtualHost></pre>

This is what port is https://site.net is listening and the normal configuration what virtualhost needs.

<pre><VirtualHost *:443>
        ServerName site.net
        ServerAlias www.site.net
        DocumentRoot "/home/user/public_html/wordpress/"</pre>

Here is the SLL Certificate configurations

<pre>        SSLCertificateFile /etc/apache2/ssl/apache.crt
        SSLCertificateKeyFile /etc/apache2/ssl/apache.key
        SSLEngine on
</VirtualHost></pre>

Restart your Apache to update new configurations.

<pre>$ sudo service apache2 restart</pre>

## Using many https sites

If you use many https sites you get this kinda notice when you restart Apache:

<pre>[warn] _default_ VirtualHost overlap on port 443, the first has precedence</pre>

You can get this warning off with adding this line to ports.conf

<pre>$ sudoedit /etc/apache2/ports.conf

NameVirtualHost *:443</pre>

Now ports.conf looks something like this:

<pre>NameVirtualHost *:80
Listen 80

<IfModule mod_ssl.c>
    NameVirtualHost *:443
    Listen 443
</IfModule>

<IfModule mod_gnutls.c>
    Listen 443
</IfModule></pre>

Restart service and now warning should be away.

<pre>$ sudo service apache2 restart</pre>

## Testing site

Go to your site.net. It should redirect you to https://site.net.

Then you get warning like this:  
[![sitenetcertificate]({{ site.baseurl }}/assets/2015/11/sitenetcertificate.png)](http://soivi.net/wp-content/uploads/2015/11/sitenetcertificate.png)

This warning comes because you use Self-Signed SSL Certificate. That means browser can't confirm your certificate and there for can't trust your certificate. You get this off by purchasing trusted certificate.

Getting to your site using Firefox:  
Click I Understand the Risks - Add Exception - Confirm Security Expetion.  
Now your site.net should be opening and using your Self-Signed SSL Certificate.

On url you can see icon of the lock and that means you are using https.  
[![soivinetlock]({{ site.baseurl }}/assets/2015/11/soivinetlock.png)](http://soivi.net/wp-content/uploads/2015/11/soivinetlock.png)

Source:  
https://www.digitalocean.com/community/tutorials/how-to-create-a-ssl-certificate-on-apache-for-ubuntu-14-04