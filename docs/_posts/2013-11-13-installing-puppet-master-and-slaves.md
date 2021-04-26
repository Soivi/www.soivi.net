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
<p><strong>If you haven't seen my previous tutorials you should see them:<br />
<a href="http://soivi.net/2013/how-to-install-puppet/">How to install Puppet</a>, <a href="http://soivi.net/2013/template-hello-world-module-to-puppet/">Hello World module using template to Puppet</a>,<br />
<a href="http://soivi.net/2013/installing-apache-and-php-with-puppet-module/">Installing Apache and PHP with Puppet module</a>.</strong></p>
<p>I’m using Xubuntu 12.04.03 32bit</p>
<p>Here is tutorial how you create PuppetMaster and two slaves. And how to use three modules using nodes to determine slaves to use different modules.</p>
<p>These are my steps:<br />
First create PuppetMaster, one slave.<br />
Second make sure simple hello module works.<br />
Thirt create new slave and make sure hello module works.<br />
Final create modules what you want and add nodes to determine what slaves uses what modules.</p>
<p>You'll should install ssh for you master and slave1.</p>
<pre>
master$ sudo apt-get update
slave1$ sudo apt-get update
master$ sudo apt-get install openssh-server
slave1$ sudo apt-get install openssh-server </pre>
<p>You can browse for mDNS/DNS-SD services using the Avahi daemon with command</p>
<pre>avahi-browse -ac |less</pre>
<p>Take ssh connection and test you can ping your master to slave and slave to master.</p>
<pre>
master$ ssh slave1@pc13.local
master$ ping -c 1 pc13.local
slave1$ ping -c 1 pc12.local
</pre>
<p>Install PuppetMaster, stop it, remove old certificates and modify puppet.conf.</p>
<pre>master$ sudo apt-get -y install puppetmaster
master$ sudo service puppetmaster stop
master$ sudo rm -r /var/lib/puppet/ssl
master$ sudoedit /etc/puppet/puppet.conf</pre>
<p>In conf file you add these lines. ( pc12 is name your masters $hostname )</p>
<pre>
[master]
dns_alt_names = puppet, pc12.local
</pre>
<p>Start PuppetMaster</p>
<pre>master$ sudo service puppetmaster start</pre>
<p>Install Puppet to your slave and edit conf file</p>
<pre>
slave1$ sudo apt-get -y install puppet
slave1$ sudoedit /etc/puppet/puppet.conf
</pre>
<p>Add to conf file your masters hostname</p>
<pre>
[agent]
server = pc12.local</pre>
<p>Edit your Puppet to start when booting</p>
<pre>
slave1$ sudoedit /etc/default/puppet

START=yes
</pre>
<p>Restart Puppet</p>
<pre>
slave1$ sudo service puppet restart
</pre>
<p>When you have restarted your slave's Puppet your master needs to confirm connection between slave and master</p>
<pre>
master$ sudo puppet cert --list
master$ sudo puppet cert --sign pc13.foo.bar.com
</pre>
<p>Make hellotest module to Puppet</p>
<pre>
master$ cd /etc/puppet
master$ sudo mkdir -p modules/hellotest/manifests/
master$ sudoedit modules/hellotest/manifests/init.pp

class hellotest {
    file { '/tmp/testModule':
        content => "Come visit Soivi.net!\n"
    } 
}
</pre>
<p>Test that your hellotest module works in your master before sharing it with your slaves</p>
<pre>
master$ puppet apply --modulepath modules/ -e 'class {"hellotest":}'
$ cat /tmp/testModule
Come visit Soivi.net!
</pre>
<p>Hellotest module works so you can share it with your slaves</p>
<pre>
master$ sudoedit manifests/site.pp

class {"hellotest":}
</pre>
<p>Restart Puppet with your slave so your slave searches new changes what master have done. ( You slaves reloads automatically changes, but now we don't want to wait it. So that's why we kick slave1 )</p>
<pre>
slave1$ sudo service puppet restart

slave1$ cat /tmp/testModule
Come visit Soivi.net!
</pre>
<p>Now we are confirmed that master and slave1 is working correctly. Now we can configure slave2 working too.</p>
<p>Install ssh, take connection and make sure your ping is working both ways.</p>
<pre>
slave2$ sudo apt-get update
slave2$ sudo apt-get install openssh-server
master$ ssh slave2@pc11.local
master$ ping -c 1 pc11.local
slave2$ ping -c 1 pc12.local
</pre>
<p>Install puppet, modify conf file and add same lines that you added before to slave1</p>
<pre>
slave2$ sudo apt-get -y install puppet
slave2$ sudoedit /etc/puppet/puppet.conf

[agent]
server = pc12.local
</pre>
<p>Configure puppet to start when booting</p>
<pre>
slave2$ sudoedit /etc/default/puppet

START=yes
</pre>
<p>And restart Puppet</p>
<pre>
slave2$ sudo service puppet restart
</pre>
<p>Now you should see in you master that slave2 is trying to connect you. Confirm it.</p>
<pre>
master$ sudo puppet cert --list
master$ sudo puppet cert --sign pc11.foo.bar.com
</pre>
<p>Reload Puppet in your slave2 and your hellotest module should work.</p>
<pre>
slave2$ sudo service puppet restart
slave2$ cat /tmp/testModule
Come visit Soivi.net!
</pre>
<p>Now lets make three different modules</p>
<p>First module installs LibreOffice</p>
<pre>
master$ sudo mkdir -p modules/libreoffice/manifests
master$ sudoedit modules/libreoffice/manifests/init.pp

class libreoffice {
        package {'libreoffice':
                ensure => present,
        }
}
</pre>
<p>Second installs VLC</p>
<pre>
master$ sudo mkdir -p modules/vlc/manifests
master$ sudoedit modules/vlc/manifests/init.pp

class vlc {
        package {'vlc':
                ensure => present,
        }
}
</pre>
<p>Third one is installing Inkscape</p>
<pre>master$ sudo mkdir -p modules/inkscape/manifests
master$ sudoedit modules/inkscape/manifests/init.pp

class inkscape {
        package {'inkscape':
                ensure => present,
        }
}
</pre>
<p>Add to site.pp your three new modules and let's make two nodes.</p>
<pre>master$ sudoedit manifests/site.pp</pre>
<p>First node makes slave1 install LibreOffice and VLC, but not Inkscape<br />
Second node makes slave2 install  LibreOffice and Inkscape, but not VLC</p>
<pre>
class {"libreoffice":}
node 'pc13.foo.bar.com' {
        class {"vlc":}
}
node 'pc11.foo.bar.com' {
        class {"inkscape":}
}
</pre>
<p>Because we don't want to wait slaves automatically reload we do it manually.</p>
<pre>
slave1$ sudo service puppet reload 
slave2$ sudo service puppet reload 
</pre>
<p>Slave1 installed softwares<br />
<a href="http://soivi.net/wp-content/uploads/2013/11/slave1.png"><img src="{{ site.baseurl }}/assets/2013/11/slave1.png" alt="slave1" width="1370" height="525" class="alignnone size-full wp-image-243" /></a></p>
<p>Slave2 installed softwares<br />
<a href="http://soivi.net/wp-content/uploads/2013/11/slave2.png"><img src="{{ site.baseurl }}/assets/2013/11/slave2.png" alt="slave2" width="1294" height="526" class="alignnone size-full wp-image-245" /></a></p>
<p>Under /etc/puppet your folder/file tree now looks like this.</p>
<pre>
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
</pre>
<p>Sources:<br />
<a href="http://docs.puppetlabs.com/learning/agent_master_basic.html">Learning Puppet — Basic Agent/Master Puppet</a><br />
<a href="http://terokarvinen.com/2012/puppetmaster-on-ubuntu-12-04">PuppetMaster on Ubuntu 12.04</a></p>
<p>This post is part of <a href="http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013">course</a></p>