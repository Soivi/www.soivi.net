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
I'm using Xubuntu 12.04.03 32bit  
[What is Fabric?](http://docs.fabfile.org/en/1.8/#about)

In this tutorial I'm using fabric to:  
- make whoami command to every user  
- method that puts file/folder to user  
- method that gets file/folder from user

I have done 4 users and created public key authentication to them. You can see how I have done it from this tutorial:  
[Public key authentication over SSH](http://soivi.net/2013/public-key-authentication-over-ssh/)

Install Fabric from apt

<pre>$ sudo apt-get update && sudo apt-get -y install fabric
</pre>

Or you can get it from pip.

<pre>$ sudo apt-get update && sudo apt-get -y install python-pip
$ sudo pip install fabric
</pre>

## Whoami

Create fabric folder and fabfile.py

<pre>$ mkdir fabric
$ cd fabric/
$ nano fabfile.py
</pre>

Create fabfile what runs command whoami to every user over ssh.

<pre>from fabric.api import env, run

env.hosts=["soivite01@localhost", "soivite02@localhost",
                "soivite03@localhost", "soivite04@localhost"]

#env.password="password"
env.parallel=True

def whoami_check():
    run("whoami")
</pre>

With fab -l you can see all commands

<pre>$ fab -l

Available commands:
    whoami_check
</pre>

Run whoami_check with fabric.

<pre>$ fab whoami_check

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

Using time before the command you can see how much time is used to run fabric command.

<pre>$ time fab whoami_check

...
real	0m2.234s
user	0m0.692s
sys	0m0.060s
</pre>

## Put and get files

Create example file that you can put to users and get that same file from users.

<pre>$ nano sendFile.txt

Hello World
</pre>

Modify fabfile. Add file_put and file_get methods.

<pre>$ nano fabfile.py
</pre>

Add put and get to imports

<pre>from fabric.api import env, run, put, get
</pre>

If you don't want to give every import by hand you can use this too

<pre>from fabric.api import *
</pre>

Add file_put and file_get methods to end of the class.

<pre>...

def file_put():
    put("sendFile.txt")

def file_get():
    get("sendFile.txt")
</pre>

Run file_put so your adding files to users.

<pre>$ fab file_put

[soivite01@localhost] Executing task 'file_put'
[soivite01@localhost] put: sendFile.txt -> /home/soivite01/sendFile.txt
...
Done.
Disconnecting from soivite01@localhost... done.
...
</pre>

Run file_get to get same files what you have added.

<pre>$ fab file_get

[soivite01@localhost] Executing task 'file_get'
[soivite01@localhost] download: /home/xubuntu/fabric/soivite01@localhost/sendFile.txt <- /home/soivite01/sendFile.txt
...
Done.
Disconnecting from soivite01@localhost... done.
...
</pre>

Your fabric folder now looks like this.

<pre>fabric/
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

## Put and get folders

Create folder and couple files in there

<pre>$ mkdir testFolder && touch testFolder/file1 testFolder/file2
</pre>

Modify fabfile

<pre>$ nano fabfile.py
</pre>

Create method that puts folder to users and gets it.

<pre>...

def folder():
    put("testFolder")
    get("testFolder")
</pre>

Run command

<pre>$ fab folder

[soivite01@localhost] Executing task 'folder'
[soivite01@localhost] put: testFolder/file2 -> /home/soivite01/testFolder/file2
[soivite01@localhost] put: testFolder/file1 -> /home/soivite01/testFolder/file1
[soivite01@localhost] download: /home/xubuntu/fabric/soivite01@localhost/testFolder/file1 <- /home/soivite01/testFolder/file1
[soivite01@localhost] download: /home/xubuntu/fabric/soivite01@localhost/testFolder/file2 <- /home/soivite01/testFolder/file2
...
</pre>

Your folder tree looks like this.

<pre>fabric/
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

Your fabfile.py looks like this

<pre>from fabric.api import env, run, put, get

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

Next tutorial you learn how to use sudo:  
[Using sudo in Fabric](http://soivi.net/2013/using-sudo-in-fabric/)

Source:  
[Fabric tutorial 1 – Take command of your network](http://awaseroot.wordpress.com/2012/04/23/fabric-tutorial-1-take-command-of-your-network/)

This post is part of [course](http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013)