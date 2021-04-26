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

{% highlight shell %}$ sudo a2enmod ssl{% endhighlight %}

Create folder where you add certificates

{% highlight shell %}$ sudo mkdir /etc/apache2/ssl{% endhighlight %}

Create certificates

{% highlight shell %}$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt{% endhighlight %}

Then you need to add your information. (Important is to add in Common name your REAL address):

{% highlight shell %}Country Name (2 letter code) [AU]:FI
State or Province Name (full name) [Some-State]:Uusimaa
Locality Name (eg, city) []:Helsinki
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:*site.net
Email Address []:your@email.fi{% endhighlight %}

You can use wild cards in Common Name by adding *. So *site.net concludes hello.site.net and site.net. This way you don't need to create and add different certificates to different sites.

## Activate your website configuration to use Certificate

Edit your site so it uses new certificate

{% highlight shell %}$ sudoedit /etc/apache2/sites-available/site.net

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
</VirtualHost>{% endhighlight %}

This is for redirection. If you try to open http://site.net you will be redirected to https://site.net

{% highlight shell %}<VirtualHost *:80>
        ServerName site.net
        ServerAlias www.site.net
        Redirect "/" "https://site.net/"
</VirtualHost>{% endhighlight %}

This is what port is https://site.net is listening and the normal configuration what virtualhost needs.

{% highlight shell %}<VirtualHost *:443>
        ServerName site.net
        ServerAlias www.site.net
        DocumentRoot "/home/user/public_html/wordpress/"{% endhighlight %}

Here is the SLL Certificate configurations

{% highlight shell %}        SSLCertificateFile /etc/apache2/ssl/apache.crt
        SSLCertificateKeyFile /etc/apache2/ssl/apache.key
        SSLEngine on
</VirtualHost>{% endhighlight %}

Restart your Apache to update new configurations.

{% highlight shell %}$ sudo service apache2 restart{% endhighlight %}

## Using many https sites

If you use many https sites you get this kinda notice when you restart Apache:

{% highlight shell %}[warn] _default_ VirtualHost overlap on port 443, the first has precedence{% endhighlight %}

You can get this warning off with adding this line to ports.conf

{% highlight shell %}$ sudoedit /etc/apache2/ports.conf

NameVirtualHost *:443{% endhighlight %}

Now ports.conf looks something like this:

{% highlight shell %}NameVirtualHost *:80
Listen 80

<IfModule mod_ssl.c>
    NameVirtualHost *:443
    Listen 443
</IfModule>

<IfModule mod_gnutls.c>
    Listen 443
</IfModule>{% endhighlight %}

Restart service and now warning should be away.

{% highlight shell %}$ sudo service apache2 restart{% endhighlight %}

## Testing site

Go to your site.net. It should redirect you to https://site.net.

Then you get warning like this:  
![sitenetcertificate](/assets/2015/11/sitenetcertificate.png)

This warning comes because you use Self-Signed SSL Certificate. That means browser can't confirm your certificate and there for can't trust your certificate. You get this off by purchasing trusted certificate.

Getting to your site using Firefox:  
Click I Understand the Risks - Add Exception - Confirm Security Expetion.  
Now your site.net should be opening and using your Self-Signed SSL Certificate.

On url you can see icon of the lock and that means you are using https.  
![sitenetcertsoivinetlockificate](/assets/2015/11/soivinetlock.png)

Source:  
https://www.digitalocean.com/community/tutorials/how-to-create-a-ssl-certificate-on-apache-for-ubuntu-14-04