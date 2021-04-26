---
layout: post
title: Public key authentication over SSH
date: 2013-10-30 15:11:40.000000000 +02:00
categories:
- Linux
- Linux centralized management
tags:
- Bash
- Bash loop
- Debian
- Linux
- Public key authentication
- SSH
- Ubuntu
- Ubuntu 12.04
- Xubuntu
- Xubuntu 12.04
permalink: "/2013/public-key-authentication-over-ssh/"
---
I'm using Xubuntu 12.04.03 32bit

**If you want to use public key authentication over SSH in your server you can just change localhost with your servers IP address.**

First you need to install openssh-server.

<pre>$ sudo apt-get update && sudo apt-get install -y openssh-server</pre>

Make user and test your ssh works.

<pre>$ sudo adduser soivite01
$ ssh soivite01@localhost whoami;
soivite01</pre>

Make public and private keys.

<pre>$ ssh-keygen</pre>

You can leave these questions empty if you don't want to change the directory where keys are saved and if you dont' want to use passphrase when you take ssh connection.

<pre>Generating public/private rsa key pair.
Enter file in which to save the key (/home/feelix/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again:</pre>

Copy public key to user and test it works

<pre>$ ssh-copy-id soivite01@localhost
$ ssh soivite01@localhost whoami;
soivite01</pre>

Make three new users

<pre>$ sudo adduser soivite02
$ sudo adduser soivite03
$ sudo adduser soivite04</pre>

Let's make public key copying to new users in loop.

<pre>$ for H in 02 03 04; do ssh-copy-id soivite$H@localhost; done

soivite02@localhost's password: 
Now try logging into the machine, with "ssh 'soivite02@localhost'", and check in:
  ~/.ssh/authorized_keys
to make sure we haven't added extra keys that you weren't expecting.
.....</pre>

Let's make shell script that connects to all users and makes whoami so we can confirm that users and ssh connection is working.

<pre>$ nano sshPublicTest.sh

#!/bin/bash/

for H in 01 02 03 04
do
ssh soivite$H@localhost whoami
done</pre>

Run shell script

<pre>$ sh sshPublicTest.sh 
soivite01
soivite02
soivite03
soivite04</pre>

Now you are successfully using public key authentication over SSH with four different users.

Or without do - done

<pre>$ nano sshPublicTest.sh

#!/bin/bash/

for H in 01 02 03 04;
{
ssh soivite$H@localhost whoami;
}</pre>

Run script

<pre>$ bash sshPublicTest.sh 
soivite01
soivite02
soivite03
soivite04</pre>

This post is part of [course](http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013)