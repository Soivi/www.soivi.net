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
[How to install Puppet](/2013/how-to-install-puppet/), [Hello World module using template to Puppet](/2013/template-hello-world-module-to-puppet/),  
[Installing Apache and PHP with Puppet module](/2013/installing-apache-and-php-with-puppet-module/).**

I’m using Xubuntu 12.04.03 32bit

Here is tutorial how you create PuppetMaster and two slaves. And how to use three modules using nodes to determine slaves to use different modules.

These are my steps:  
First create PuppetMaster, one slave.  
Second make sure simple hello module works.  
Thirt create new slave and make sure hello module works.  
Final create modules what you want and add nodes to determine what slaves uses what modules.

You'll should install ssh for you master and slave1.

{% highlight shell %}
master$ sudo apt-get update
slave1$ sudo apt-get update
master$ sudo apt-get install openssh-server
slave1$ sudo apt-get install openssh-server
{% endhighlight %}

You can browse for mDNS/DNS-SD services using the Avahi daemon with command

{% highlight shell %}
avahi-browse -ac |less
{% endhighlight %}

Take ssh connection and test you can ping your master to slave and slave to master.

{% highlight shell %}
master$ ssh slave1@pc13.local
master$ ping -c 1 pc13.local
slave1$ ping -c 1 pc12.local
{% endhighlight %}

Install PuppetMaster, stop it, remove old certificates and modify puppet.conf.

{% highlight shell %}
master$ sudo apt-get -y install puppetmaster
master$ sudo service puppetmaster stop
master$ sudo rm -r /var/lib/puppet/ssl
master$ sudoedit /etc/puppet/puppet.conf
{% endhighlight %}

In conf file you add these lines. ( pc12 is name your masters $hostname )

{% highlight shell %}
[master]
dns_alt_names = puppet, pc12.local
{% endhighlight %}

Start PuppetMaster

{% highlight shell %}
master$ sudo service puppetmaster start
{% endhighlight %}

Install Puppet to your slave and edit conf file

{% highlight shell %}
slave1$ sudo apt-get -y install puppet
slave1$ sudoedit /etc/puppet/puppet.conf
{% endhighlight %}

Add to conf file your masters hostname

{% highlight shell %}
[agent]
server = pc12.local
{% endhighlight %}

Edit your Puppet to start when booting

{% highlight shell %}
slave1$ sudoedit /etc/default/puppet

START=yes
{% endhighlight %}

Restart Puppet

{% highlight shell %}
slave1$ sudo service puppet restart
{% endhighlight %}

When you have restarted your slave's Puppet your master needs to confirm connection between slave and master

{% highlight shell %}
master$ sudo puppet cert --list
master$ sudo puppet cert --sign pc13.foo.bar.com
{% endhighlight %}

Make hellotest module to Puppet

{% highlight shell %}
master$ cd /etc/puppet
master$ sudo mkdir -p modules/hellotest/manifests/
master$ sudoedit modules/hellotest/manifests/init.pp

class hellotest {
    file { '/tmp/testModule':
        content => "Come visit Soivi.net!\n"
    } 
}
{% endhighlight %}

Test that your hellotest module works in your master before sharing it with your slaves

{% highlight shell %}
master$ puppet apply --modulepath modules/ -e 'class {"hellotest":}'
$ cat /tmp/testModule
Come visit Soivi.net!
{% endhighlight %}

Hellotest module works so you can share it with your slaves

{% highlight shell %}
master$ sudoedit manifests/site.pp

class {"hellotest":}
{% endhighlight %}

Restart Puppet with your slave so your slave searches new changes what master have done. ( You slaves reloads automatically changes, but now we don't want to wait it. So that's why we kick slave1 )

{% highlight shell %}
slave1$ sudo service puppet restart

slave1$ cat /tmp/testModule
Come visit Soivi.net!
{% endhighlight %}

Now we are confirmed that master and slave1 is working correctly. Now we can configure slave2 working too.

Install ssh, take connection and make sure your ping is working both ways.

{% highlight shell %}
slave2$ sudo apt-get update
slave2$ sudo apt-get install openssh-server
master$ ssh slave2@pc11.local
master$ ping -c 1 pc11.local
slave2$ ping -c 1 pc12.local
{% endhighlight %}

Install puppet, modify conf file and add same lines that you added before to slave1

{% highlight shell %}
slave2$ sudo apt-get -y install puppet
slave2$ sudoedit /etc/puppet/puppet.conf

[agent]
server = pc12.local
{% endhighlight %}

Configure puppet to start when booting

{% highlight shell %}
slave2$ sudoedit /etc/default/puppet

START=yes
{% endhighlight %}

And restart Puppet

{% highlight shell %}
slave2$ sudo service puppet restart
{% endhighlight %}

Now you should see in you master that slave2 is trying to connect you. Confirm it.

{% highlight shell %}
master$ sudo puppet cert --list
master$ sudo puppet cert --sign pc11.foo.bar.com
{% endhighlight %}

Reload Puppet in your slave2 and your hellotest module should work.

{% highlight shell %}
slave2$ sudo service puppet restart
slave2$ cat /tmp/testModule
Come visit Soivi.net!
{% endhighlight %}

Now lets make three different modules

First module installs LibreOffice

{% highlight shell %}
master$ sudo mkdir -p modules/libreoffice/manifests
master$ sudoedit modules/libreoffice/manifests/init.pp

class libreoffice {
        package {'libreoffice':
                ensure => present,
        }
}
{% endhighlight %}

Second installs VLC

{% highlight shell %}
master$ sudo mkdir -p modules/vlc/manifests
master$ sudoedit modules/vlc/manifests/init.pp

class vlc {
        package {'vlc':
                ensure => present,
        }
}
{% endhighlight %}

Third one is installing Inkscape

{% highlight shell %}
master$ sudo mkdir -p modules/inkscape/manifests
master$ sudoedit modules/inkscape/manifests/init.pp

class inkscape {
        package {'inkscape':
                ensure => present,
        }
}
{% endhighlight %}

Add to site.pp your three new modules and let's make two nodes.

{% highlight shell %}
master$ sudoedit manifests/site.pp
{% endhighlight %}

First node makes slave1 install LibreOffice and VLC, but not Inkscape  
Second node makes slave2 install LibreOffice and Inkscape, but not VLC

{% highlight shell %}
class {"libreoffice":}
node 'pc13.foo.bar.com' {
        class {"vlc":}
}
node 'pc11.foo.bar.com' {
        class {"inkscape":}
}
{% endhighlight %}

Because we don't want to wait slaves automatically reload we do it manually.

{% highlight shell %}
slave1$ sudo service puppet reload 
slave2$ sudo service puppet reload 
{% endhighlight %}

Slave1 installed softwares  
![slave1](/assets/2013/11/slave1.png)

Slave2 installed softwares  
![slave2](/assets/2013/11/slave2.png)

Under /etc/puppet your folder/file tree now looks like this.

{% highlight shell %}
puppet/
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
{% endhighlight %}

Sources:  
[Learning Puppet — Basic Agent/Master Puppet](http://docs.puppetlabs.com/learning/agent_master_basic.html)  
[PuppetMaster on Ubuntu 12.04](http://terokarvinen.com/2012/puppetmaster-on-ubuntu-12-04)
