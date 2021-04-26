---
layout: post
title: Using sudo in Fabric
date: 2013-12-15 14:27:50.000000000 +02:00
categories:
- Linux
- Linux centralized management
tags:
- Debian
- Fabric
- Linux
- Sudo
- Ubuntu
- Ubuntu 12.04
- Xubuntu
- Xubuntu 12.04
permalink: "/2013/using-sudo-in-fabric/"
---
<p>I’m using Xubuntu 12.04.03 32bit</p>
<p>If you haven't seen my previous tutorial you should see it:<br />
<a href="http://soivi.net/2013/fabric-hello-world/">Fabric hello world</a></p>
<p>In this tutorial I'm using sudo to copy logs.</p>
<p>Create folder and fabfile.py.</p>
<pre>
$ mkdir sudofabric && cd sudofabric
$ nano fabfile.py
</pre>
<p>Create two methods: super and superfolder.</p>
<pre>
from fabric.api import  env, run, get, sudo

env.hosts=["soivite01@localhost", "soivite02@localhost",
                "soivite03@localhost", "soivite04@localhost"]

#env.password="password"
env.parallel=True

def super():
    sudo("cp /var/log/syslog $HOME")
    me = run("whoami")
    sudo("chown -R "+me+" /home/"+me)
    get("syslog")

def superfolder():
    sudo("cp -r /var/log/apt/ $HOME")
    me = run("whoami")
    sudo("chown -R "+me+" /home/"+me)
    get("apt")
</pre>
<p>Modify sudoers file. (WARNING: modifying wrong or giving rights to wrong person could compromise your computer)</p>
<pre>
$ sudo visudo
</pre>
<p>Because I don't want to give password everytime running fabfile I give rights to all 4 user to run sudo without password. I added these lines to end of sudoers file</p>
<pre>
soivite01 ALL=(ALL) NOPASSWD: ALL
soivite02 ALL=(ALL) NOPASSWD: ALL
soivite03 ALL=(ALL) NOPASSWD: ALL
soivite04 ALL=(ALL) NOPASSWD: ALL
</pre>
<p>$ Run super command.</p>
<pre>
$ fab super

[soivite01@localhost] Executing task 'super'
[soivite01@localhost] sudo: cp /var/log/syslog $HOME
[soivite01@localhost] run: whoami
[soivite01@localhost] out: soivite01
[soivite01@localhost] sudo: chown -R soivite01 /home/soivite01
[soivite01@localhost] download: /home/xubuntu/fabric/soivite01@localhost/syslog <- /home/soivite01/syslog
...
</pre>
<p>Run superfolder command.</p>
<pre>
$ fab superfolder
</pre>
<p>Your folder tree should look like this.</p>
<pre>
sudofabric/
├── fabfile.py
├── fabfile.pyc
├── soivite01@localhost
│   ├── apt
│   │   ├── history.log
│   │   └── term.log
│   └── syslog
├── soivite02@localhost
│   ├── apt
│   │   ├── history.log
│   │   └── term.log
│   └── syslog
├── soivite03@localhost
│   ├── apt
│   │   ├── history.log
│   │   └── term.log
│   └── syslog
└── soivite04@localhost
    ├── apt
    │   ├── history.log
    │   └── term.log
    └── syslog
</pre>
<p>Source:<br />
<a href="http://awaseroot.wordpress.com/2012/04/23/fabric-tutorial-1-take-command-of-your-network/">Fabric tutorial 1 – Take command of your network</a></p>
<p>This post is part of <a href="http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013">course</a></p>