---
layout: post
title: Installing Puppet master and slaves
date: 2013-11-13 17:54:22.000000000 +02:00
categories:
- Linux
- Linux centralized management
tags:
- Debian
- Linux
- Master
- Module
- Node
- Puppet
- PuppetMaster
- PuppetSlave
- Slave
- Ubuntu
- Ubuntu 12.04
- Xubuntu
- Xubuntu 12.04
permalink: "/2013/installing-puppet-master-and-slaves/"
---
**If you haven't seen my previous tutorials you should see them:  
[How to install Puppet](http://soivi.net/2013/how-to-install-puppet/), [Hello World module using template to Puppet](http://soivi.net/2013/template-hello-world-module-to-puppet/),  
[Installing Apache and PHP with Puppet module](http://soivi.net/2013/installing-apache-and-php-with-puppet-module/).**

I’m using Xubuntu 12.04.03 32bit

Here is tutorial how you create PuppetMaster and two slaves. And how to use three modules using nodes to determine slaves to use different modules.

These are my steps:  
First create PuppetMaster, one slave.  
Second make sure simple hello module works.  
Thirt create new slave and make sure hello module works.  
Final create modules what you want and add nodes to determine what slaves uses what modules.

You'll should install ssh for you master and slave1.

<pre>master$ sudo apt-get update
slave1$ sudo apt-get update
master$ sudo apt-get install openssh-server
slave1$ sudo apt-get install openssh-server </pre>

You can browse for mDNS/DNS-SD services using the Avahi daemon with command

<pre>avahi-browse -ac |less</pre>

Take ssh connection and test you can ping your master to slave and slave to master.

<pre>master$ ssh slave1@pc13.local
master$ ping -c 1 pc13.local
slave1$ ping -c 1 pc12.local
</pre>

Install PuppetMaster, stop it, remove old certificates and modify puppet.conf.

<pre>master$ sudo apt-get -y install puppetmaster
master$ sudo service puppetmaster stop
master$ sudo rm -r /var/lib/puppet/ssl
master$ sudoedit /etc/puppet/puppet.conf</pre>

In conf file you add these lines. ( pc12 is name your masters $hostname )

<pre>[master]
dns_alt_names = puppet, pc12.local
</pre>

Start PuppetMaster

<pre>master$ sudo service puppetmaster start</pre>

Install Puppet to your slave and edit conf file

<pre>slave1$ sudo apt-get -y install puppet
slave1$ sudoedit /etc/puppet/puppet.conf
</pre>

Add to conf file your masters hostname

<pre>[agent]
server = pc12.local</pre>

Edit your Puppet to start when booting

<pre>slave1$ sudoedit /etc/default/puppet

START=yes
</pre>

Restart Puppet

<pre>slave1$ sudo service puppet restart
</pre>

When you have restarted your slave's Puppet your master needs to confirm connection between slave and master

<pre>master$ sudo puppet cert --list
master$ sudo puppet cert --sign pc13.foo.bar.com
</pre>

Make hellotest module to Puppet

<pre>master$ cd /etc/puppet
master$ sudo mkdir -p modules/hellotest/manifests/
master$ sudoedit modules/hellotest/manifests/init.pp

class hellotest {
    file { '/tmp/testModule':
        content => "Come visit Soivi.net!\n"
    } 
}
</pre>

Test that your hellotest module works in your master before sharing it with your slaves

<pre>master$ puppet apply --modulepath modules/ -e 'class {"hellotest":}'
$ cat /tmp/testModule
Come visit Soivi.net!
</pre>

Hellotest module works so you can share it with your slaves

<pre>master$ sudoedit manifests/site.pp

class {"hellotest":}
</pre>

Restart Puppet with your slave so your slave searches new changes what master have done. ( You slaves reloads automatically changes, but now we don't want to wait it. So that's why we kick slave1 )

<pre>slave1$ sudo service puppet restart

slave1$ cat /tmp/testModule
Come visit Soivi.net!
</pre>

Now we are confirmed that master and slave1 is working correctly. Now we can configure slave2 working too.

Install ssh, take connection and make sure your ping is working both ways.

<pre>slave2$ sudo apt-get update
slave2$ sudo apt-get install openssh-server
master$ ssh slave2@pc11.local
master$ ping -c 1 pc11.local
slave2$ ping -c 1 pc12.local
</pre>

Install puppet, modify conf file and add same lines that you added before to slave1

<pre>slave2$ sudo apt-get -y install puppet
slave2$ sudoedit /etc/puppet/puppet.conf

[agent]
server = pc12.local
</pre>

Configure puppet to start when booting

<pre>slave2$ sudoedit /etc/default/puppet

START=yes
</pre>

And restart Puppet

<pre>slave2$ sudo service puppet restart
</pre>

Now you should see in you master that slave2 is trying to connect you. Confirm it.

<pre>master$ sudo puppet cert --list
master$ sudo puppet cert --sign pc11.foo.bar.com
</pre>

Reload Puppet in your slave2 and your hellotest module should work.

<pre>slave2$ sudo service puppet restart
slave2$ cat /tmp/testModule
Come visit Soivi.net!
</pre>

Now lets make three different modules

First module installs LibreOffice

<pre>master$ sudo mkdir -p modules/libreoffice/manifests
master$ sudoedit modules/libreoffice/manifests/init.pp

class libreoffice {
        package {'libreoffice':
                ensure => present,
        }
}
</pre>

Second installs VLC

<pre>master$ sudo mkdir -p modules/vlc/manifests
master$ sudoedit modules/vlc/manifests/init.pp

class vlc {
        package {'vlc':
                ensure => present,
        }
}
</pre>

Third one is installing Inkscape

<pre>master$ sudo mkdir -p modules/inkscape/manifests
master$ sudoedit modules/inkscape/manifests/init.pp

class inkscape {
        package {'inkscape':
                ensure => present,
        }
}
</pre>

Add to site.pp your three new modules and let's make two nodes.

<pre>master$ sudoedit manifests/site.pp</pre>

First node makes slave1 install LibreOffice and VLC, but not Inkscape  
Second node makes slave2 install LibreOffice and Inkscape, but not VLC

<pre>class {"libreoffice":}
node 'pc13.foo.bar.com' {
        class {"vlc":}
}
node 'pc11.foo.bar.com' {
        class {"inkscape":}
}
</pre>

Because we don't want to wait slaves automatically reload we do it manually.

<pre>slave1$ sudo service puppet reload 
slave2$ sudo service puppet reload 
</pre>

Slave1 installed softwares  
[![slave1]({{ site.baseurl }}/assets/2013/11/slave1.png)](http://soivi.net/wp-content/uploads/2013/11/slave1.png)

Slave2 installed softwares  
[![slave2]({{ site.baseurl }}/assets/2013/11/slave2.png)](http://soivi.net/wp-content/uploads/2013/11/slave2.png)

Under /etc/puppet your folder/file tree now looks like this.

<pre>puppet/
├── auth.conf
├── etckeeper-commit-post
├── etckeeper-commit-pre
├── fileserver.conf
├── manifests
│   └── site.pp
├── modules
│   ├── hellotest
│   │   └── manifests
│   │       └── init.pp
│   ├── inkscape
│   │   └── manifests
│   │       └── init.pp
│   ├── libreoffice
│   │   └── manifests
│   │       └── init.pp
│   └── vlc
│       └── manifests
│           └── init.pp
├── puppet.conf
└── templates
</pre>

Sources:  
[Learning Puppet — Basic Agent/Master Puppet](http://docs.puppetlabs.com/learning/agent_master_basic.html)  
[PuppetMaster on Ubuntu 12.04](http://terokarvinen.com/2012/puppetmaster-on-ubuntu-12-04)

This post is part of [course](http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013)