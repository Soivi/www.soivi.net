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
<p><a href="https://puppet.com/"><img src="{{ site.baseurl }}/assets/2013/11/Puppet_Logo.svg_.min_.png" alt="puppet" width="200" height="87" class="alignright size-full wp-image-1139" /></a>Tutorial guides how to install Apache and PHP with Puppet modules using Linux. I’m using Xubuntu 12.04.03 32bit. Puppet is great way to handle several computers from one computer.</p>
<p>In this tutorial there are two modules. First module is installing Apache. Second module is installing Apache2 and PHP. After both modules there are explanation how to test modules.</p>
<p><em>If you haven't seen my previous tutorials you should check them out:<br />
<a href="http://soivi.net/2013/how-to-install-puppet/">How_to_install_Puppet</a>, <a href="http://soivi.net/2013/template-hello-world-module-to-puppet/">Hello_World_module_using_Puppet_template</a>.</em></p>
<h2>Installing Apache</h2>
<p>Create folders and make init.pp file</p>
<pre>$ mkdir -p puppet/modules/apache2/manifests
$ cd puppet/
$ nano modules/apache2/manifests/init.pp</pre>
<p>Add package, service and files in to init.pp. Package is installing Apache. Files will create symlinks. This is obligatory if you want to use user directories. Files modify a2enmod so it's enabling userdirs. When Apache is installed and symlinks are created. Service is checking your Apache is really started and running. If Apache isn't running service will start it.</p>
<pre>class apache2 {
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
}</pre>
<p>Apply apache2 module.</p>
<pre>$ sudo puppet apply --modulepath modules/ -e 'class {"apache2":}'</pre>
<p>Now you have module that will install Apache, if it isn't installed. Enables user directories, if those aren't enabled. And will make sure that Apache is running.</p>
<h2>Testing Apache</h2>
<p>Let's try your module is really working. Let's make public_html folder and add to there example index.html file</p>
<pre>$ mkdir ~/public_html
$ nano ~/public_html/index.html

&lt;!DOCTYPE HTML&gt;
&lt;html&gt;
     &lt;body&gt;
          &lt;p&gt;Hello World!&lt;/p&gt;
     &lt;/body&gt;
&lt;/html&gt;</pre>
<p>Open firefox</p>
<pre>$ firefox localhost/~user/
<em>Hello World!</em></pre>
<p>Now we are successfully used Puppet modules to install Apache. In second module we are installing Apache and PHP.</p>
<h2>Installing Apache and PHP</h2>
<p>Now we create new module. Add needed folders and init.pp</p>
<pre>$ mkdir -p modules/phpwithapache/manifests
$ nano modules/phpwithapache/manifests/init.pp</pre>
<p>Create module that makes same things that first module, but also installs php5 and libapache2-mod-php5. Then module enables php5 working in user directories.</p>
<pre>class phpwithapache{
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
}</pre>
<p>Create templates folder and php5.conf.erb file there what overwrites your php5.conf file. New php5.conf file enables php5 to users. Without making these configure changes to php5.conf php5 isn't working in user directories.</p>
<pre>$ mkdir -p modules/phpwithapache/templates
$ nano modules/phpwithapache/templates/php5.conf.erb 

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
   #     &lt;Directory /home/*/public_html&gt;
   #         php_admin_value engine Off
   #     &lt;/Directory&gt;
   # &lt;/IfModule&gt;
&lt;/IfModule&gt;</pre>
<p>Apply phpwithapache module</p>
<pre>
$ sudo puppet apply --modulepath modules/ -e 'class {"phpwithapache":}'
</pre>
<h2>Testing PHP</h2>
<p>Modify index.html to index.php what is in your public_html and create example php page</p>
<pre>
$ mv ~/public_html/index.html ~/public_html/index.php
$ nano ~/public_html/index.php

&lt;!DOCTYPE html&gt;
&lt;html&gt;
     &lt;body&gt;
          &lt;h1&gt;My first PHP page&lt;/h1&gt;
          &lt;?php
              echo &quot;Hello World!&quot;;
          ?&gt;
     &lt;/body&gt;
&lt;/html&gt;</pre>
<p>Test your index.php</p>
<pre>$ firefox localhost/~user/

<em>My first PHP page
Hello World!</em> </pre>
<h2>Conclusion</h2>
<p>Now you have created two Puppet modules. First module installed Apache2. Second module installed Apache and PHP. Of course we also tested that Apache and PHP was really working correctly.</p>
<p>Here is what your folder/file tree should look like</p>
<pre>puppet/
└── modules
    ├── apache2
    │   └── manifests
    │       └── init.pp
    └── phpwithapache
        ├── manifests
        │   └── init.pp
        └── templates
            └── php5.conf.erb

6 directories, 3 files</pre>
<p>Sources:<br />
<a href="http://samuelkontiomaa.com/2013/11/01/hello-puppet-module/">Samuel Kontiomaa</a></p>
<p>This post is part of <a href="http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013">course</a></p>
<h4>What is Puppet? <a href="https://en.wikipedia.org/wiki/Puppet_(software)">Link</a></h4>
