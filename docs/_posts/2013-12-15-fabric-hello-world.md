---
layout: post
title: Fabric hello world
date: 2013-12-15 14:26:28.000000000 +02:00
categories:
- Linux
- Linux centralized management
tags:
- Debian
- Fabric
- Linux
- Ubuntu
- Ubuntu 12.04
- Xubuntu
- Xubuntu 12.04
permalink: "/2013/fabric-hello-world/"
---
<p>I'm using Xubuntu 12.04.03 32bit<br />
<a href="http://docs.fabfile.org/en/1.8/#about">What is Fabric?</a></p>
<p>In this tutorial I'm using fabric to:<br />
- make whoami command to every user<br />
- method that puts file/folder to user<br />
- method that gets file/folder from user</p>
<p>I have done 4 users and created public key authentication to them. You can see how I have done it from this tutorial:<br />
<a href="http://soivi.net/2013/public-key-authentication-over-ssh/">Public key authentication over SSH</a></p>
<p>Install Fabric from apt</p>
<pre>
$ sudo apt-get update && sudo apt-get -y install fabric
</pre>
<p>Or you can get it from pip.</p>
<pre>
$ sudo apt-get update && sudo apt-get -y install python-pip
$ sudo pip install fabric
</pre>
<h2>Whoami</h2>
<p>Create fabric folder and fabfile.py</p>
<pre>
$ mkdir fabric
$ cd fabric/
$ nano fabfile.py
</pre>
<p>Create fabfile what runs command whoami to every user over ssh.</p>
<pre>
from fabric.api import env, run

env.hosts=["soivite01@localhost", "soivite02@localhost",
                "soivite03@localhost", "soivite04@localhost"]

#env.password="password"
env.parallel=True

def whoami_check():
    run("whoami")
</pre>
<p>With fab -l you can see all commands</p>
<pre>
$ fab -l

Available commands:
    whoami_check
</pre>
<p>Run whoami_check with fabric.</p>
<pre>
$ fab whoami_check

[soivite01@localhost] Executing task 'whoami_check'
...
[soivite04@localhost] run: whoami
...
[soivite01@localhost] out: soivite01
[soivite02@localhost] out: soivite02
[soivite03@localhost] out: soivite03
[soivite04@localhost] out: soivite04
Done.
</pre>
<p>Using time before the command you can see how much time is used to run fabric command.</p>
<pre>
$ time fab whoami_check

...
real	0m2.234s
user	0m0.692s
sys	0m0.060s
</pre>
<h2>Put and get files</h2>
<p>Create example file that you can put to users and get that same file from users.</p>
<pre>
$ nano sendFile.txt

Hello World
</pre>
<p>Modify fabfile. Add file_put and file_get methods.</p>
<pre>
$ nano fabfile.py
</pre>
<p>Add put and get to imports</p>
<pre>
from fabric.api import env, run, put, get
</pre>
<p>If you don't want to give every import by hand you can use this too</p>
<pre>
from fabric.api import *
</pre>
<p>Add file_put and file_get methods to end of the class.</p>
<pre>
...

def file_put():
    put("sendFile.txt")

def file_get():
    get("sendFile.txt")
</pre>
<p>Run file_put so your adding files to users.</p>
<pre>
$ fab file_put

[soivite01@localhost] Executing task 'file_put'
[soivite01@localhost] put: sendFile.txt -> /home/soivite01/sendFile.txt
...
Done.
Disconnecting from soivite01@localhost... done.
...
</pre>
<p>Run file_get to get same files what you have added.</p>
<pre>
$ fab file_get

[soivite01@localhost] Executing task 'file_get'
[soivite01@localhost] download: /home/xubuntu/fabric/soivite01@localhost/sendFile.txt <- /home/soivite01/sendFile.txt
...
Done.
Disconnecting from soivite01@localhost... done.
...
</pre>
<p>Your fabric folder now looks like this.</p>
<pre>
fabric/
├── fabfile.py
├── fabfile.pyc
├── sendFile.txt
├── soivite01@localhost
│   └── sendFile.txt
├── soivite02@localhost
│   └── sendFile.txt
├── soivite03@localhost
│   └── sendFile.txt
└── soivite04@localhost
    └── sendFile.txt
</pre>
<h2>Put and get folders</h2>
<p>Create folder and couple files in there</p>
<pre>
$ mkdir testFolder && touch testFolder/file1 testFolder/file2
</pre>
<p>Modify fabfile</p>
<pre>
$ nano fabfile.py
</pre>
<p>Create method that puts folder to users and gets it.</p>
<pre>
...

def folder():
    put("testFolder")
    get("testFolder")
</pre>
<p>Run command</p>
<pre>
$ fab folder

[soivite01@localhost] Executing task 'folder'
[soivite01@localhost] put: testFolder/file2 -> /home/soivite01/testFolder/file2
[soivite01@localhost] put: testFolder/file1 -> /home/soivite01/testFolder/file1
[soivite01@localhost] download: /home/xubuntu/fabric/soivite01@localhost/testFolder/file1 <- /home/soivite01/testFolder/file1
[soivite01@localhost] download: /home/xubuntu/fabric/soivite01@localhost/testFolder/file2 <- /home/soivite01/testFolder/file2
...
</pre>
<p>Your folder tree looks like this.</p>
<pre>
fabric/
├── fabfile.py
├── fabfile.pyc
├── sendFile.txt
├── soivite01@localhost
│   ├── sendFile.txt
│   └── testFolder
│       ├── file1
│       └── file2
├── soivite02@localhost
│   ├── sendFile.txt
│   └── testFolder
│       ├── file1
│       └── file2
├── soivite03@localhost
│   ├── sendFile.txt
│   └── testFolder
│       ├── file1
│       └── file2
├── soivite04@localhost
│   ├── sendFile.txt
│   └── testFolder
│       ├── file1
│       └── file2
└── testFolder
    ├── file1
    └── file2
</pre>
<p>Your fabfile.py looks like this</p>
<pre>
from fabric.api import env, run, put, get

env.hosts=["soivite01@localhost", "soivite02@localhost",
                "soivite03@localhost", "soivite04@localhost"]

#env.password="password"
env.parallel=True

def whoami_check():
    run("whoami")

def file_put():
    put("sendFile.txt")

def file_get():
    get("sendFile.txt")

def folder():
    put("testFolder")
    get("testFolder")
</pre>
<p>Next tutorial you learn how to use sudo:<br />
<a href="http://soivi.net/2013/using-sudo-in-fabric/">Using sudo in Fabric</a></p>
<p>Source:<br />
<a href="http://awaseroot.wordpress.com/2012/04/23/fabric-tutorial-1-take-command-of-your-network/">Fabric tutorial 1 – Take command of your network</a></p>
<p>This post is part of <a href="http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013">course</a></p>