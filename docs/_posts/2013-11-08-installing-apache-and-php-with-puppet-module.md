---
layout: post
title: Installing Apache and PHP with Puppet module
date: 2013-11-08 12:06:57.000000000 +02:00
categories:
- Linux
- Linux centralized management
tags:
- Apache
- Debian
- Linux
- Module
- PHP
- Puppet
- Template
- Ubuntu
- Ubuntu 12.04
- Xubuntu
- Xubuntu 12.04
permalink: "/2013/installing-apache-and-php-with-puppet-module/"
---
![puppet](/assets/2013/11/Puppet_Logo.svg_.min_.png)
Tutorial guides how to install Apache and PHP with Puppet modules using Linux. I’m using Xubuntu 12.04.03 32bit. Puppet is great way to handle several computers from one computer.

In this tutorial there are two modules. First module is installing Apache. Second module is installing Apache2 and PHP. After both modules there are explanation how to test modules.

**If you haven't seen my previous tutorials you should check them out:  
[How_to_install_Puppet](/2013/how-to-install-puppet/), [Hello_World_module_using_Puppet_template](/2013/template-hello-world-module-to-puppet/).**

## Installing Apache

Create folders and make init.pp file

{% highlight shell %}
$ mkdir -p puppet/modules/apache2/manifests
$ cd puppet/
$ nano modules/apache2/manifests/init.pp
{% endhighlight %}

Add package, service and files in to init.pp. Package is installing Apache. Files will create symlinks. This is obligatory if you want to use user directories. Files modify a2enmod so it's enabling userdirs. When Apache is installed and symlinks are created. Service is checking your Apache is really started and running. If Apache isn't running service will start it.

{% highlight shell %}
class apache2 {
        package {'apache2':
                ensure => present,
        }

        service {'apache2':
                ensure => "running",
                enable => "true",
                require => Package["apache2"],
        }

        file { '/etc/apache2/mods-enabled/userdir.load':
                ensure => 'link',
                target => '/etc/apache2/mods-available/userdir.load',
                notify => Service["apache2"],
                require => Package["apache2"],
        }

        file { '/etc/apache2/mods-enabled/userdir.conf':
                ensure => 'link',
                target => '/etc/apache2/mods-available/userdir.conf',
                notify => Service["apache2"],
                require => Package["apache2"],
        }
}
{% endhighlight %}

Apply apache2 module.

{% highlight shell %}
$ sudo puppet apply --modulepath modules/ -e 'class {"apache2":}'
{% endhighlight %}

Now you have module that will install Apache, if it isn't installed. Enables user directories, if those aren't enabled. And will make sure that Apache is running.

## Testing Apache

Let's try your module is really working. Let's make public_html folder and add to there example index.html file

{% highlight shell %}
$ mkdir ~/public_html
$ nano ~/public_html/index.html

<!DOCTYPE HTML>
<html>
     <body>
          <p>Hello World!</p>
     </body>
</html>
{% endhighlight %}

Open firefox

{% highlight shell %}
$ firefox localhost/~user/
Hello World!
{% endhighlight %}

Now we are successfully used Puppet modules to install Apache. In second module we are installing Apache and PHP.

## Installing Apache and PHP

Now we create new module. Add needed folders and init.pp

{% highlight shell %}
$ mkdir -p modules/phpwithapache/manifests
$ nano modules/phpwithapache/manifests/init.pp
{% endhighlight %}

Create module that makes same things that first module, but also installs php5 and libapache2-mod-php5\. Then module enables php5 working in user directories.

{% highlight shell %}
class phpwithapache{
        package {'apache2':
                ensure => present,
        }

        file { '/etc/apache2/mods-enabled/userdir.load':
                ensure => 'link',
                target => '/etc/apache2/mods-available/userdir.load',
                notify => Service["apache2"],
                require => Package["apache2"],
        }

        file { '/etc/apache2/mods-enabled/userdir.conf':
                ensure => 'link',
                target => '/etc/apache2/mods-available/userdir.conf',
                notify => Service["apache2"],
                require => Package["apache2"],
        }

        package {'libapache2-mod-php5':
                ensure => present,
        }

        file {'/etc/apache2/mods-available/php5.conf':
                content => template("phpwithapache/php5.conf.erb"),
                require => Package["apache2"],
                notify => Service["apache2"],
        }

        service {'apache2':
                ensure => "running",
                enable => "true",
                require => Package["apache2"],
        }
}
{% endhighlight %}

Create templates folder and php5.conf.erb file there what overwrites your php5.conf file. New php5.conf file enables php5 to users. Without making these configure changes to php5.conf php5 isn't working in user directories.

{% highlight shell %}
$ mkdir -p modules/phpwithapache/templates
$ nano modules/phpwithapache/templates/php5.conf.erb 

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
   #     <Directory /home/*/public_html>
   #         php_admin_value engine Off
   #     </Directory>
   # </IfModule>
</IfModule>
{% endhighlight %}

Apply phpwithapache module

{% highlight shell %}
$ sudo puppet apply --modulepath modules/ -e 'class {"phpwithapache":}'
{% endhighlight %}

## Testing PHP

Modify index.html to index.php what is in your public_html and create example php page

{% highlight shell %}
$ mv ~/public_html/index.html ~/public_html/index.php
$ nano ~/public_html/index.php

<!DOCTYPE html>
<html>
     <body>
          <h1>My first PHP page</h1>
          <?php
              echo "Hello World!";
          ?>
     </body>
</html>
{% endhighlight %}

Test your index.php

{% highlight shell %}
$ firefox localhost/~user/

My first PHP page
Hello World!
{% endhighlight %}

## Conclusion

Now you have created two Puppet modules. First module installed Apache2\. Second module installed Apache and PHP. Of course we also tested that Apache and PHP was really working correctly.

Here is what your folder/file tree should look like

{% highlight shell %}
puppet/
└── modules
    ├── apache2
    │   └── manifests
    │       └── init.pp
    └── phpwithapache
        ├── manifests
        │   └── init.pp
        └── templates
            └── php5.conf.erb

6 directories, 3 files
{% endhighlight %}

Sources:  
[Samuel Kontiomaa](http://samuelkontiomaa.com/2013/11/01/hello-puppet-module/)

This post is part of [course](http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013)

#### What is Puppet? [Link](https://en.wikipedia.org/wiki/Puppet_(software))