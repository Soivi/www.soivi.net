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

{% highlight shell %}$ sudo apt-get update && sudo apt-get install -y openssh-server{% endhighlight %}

Make user and test your ssh works.

{% highlight shell %}$ sudo adduser soivite01
$ ssh soivite01@localhost whoami;
soivite01{% endhighlight %}

Make public and private keys.

{% highlight shell %}$ ssh-keygen{% endhighlight %}

You can leave these questions empty if you don't want to change the directory where keys are saved and if you dont' want to use passphrase when you take ssh connection.

{% highlight shell %}Generating public/private rsa key pair.
Enter file in which to save the key (/home/feelix/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again:{% endhighlight %}

Copy public key to user and test it works

{% highlight shell %}$ ssh-copy-id soivite01@localhost
$ ssh soivite01@localhost whoami;
soivite01{% endhighlight %}

Make three new users

{% highlight shell %}$ sudo adduser soivite02
$ sudo adduser soivite03
$ sudo adduser soivite04{% endhighlight %}

Let's make public key copying to new users in loop.

{% highlight shell %}$ for H in 02 03 04; do ssh-copy-id soivite$H@localhost; done

soivite02@localhost's password: 
Now try logging into the machine, with "ssh 'soivite02@localhost'", and check in:
  ~/.ssh/authorized_keys
to make sure we haven't added extra keys that you weren't expecting.
.....{% endhighlight %}

Let's make shell script that connects to all users and makes whoami so we can confirm that users and ssh connection is working.

{% highlight shell %}$ nano sshPublicTest.sh

#!/bin/bash/

for H in 01 02 03 04
do
ssh soivite$H@localhost whoami
done{% endhighlight %}

Run shell script

{% highlight shell %}$ sh sshPublicTest.sh 
soivite01
soivite02
soivite03
soivite04{% endhighlight %}

Now you are successfully using public key authentication over SSH with four different users.

Or without do - done

{% highlight shell %}$ nano sshPublicTest.sh

#!/bin/bash/

for H in 01 02 03 04;
{
ssh soivite$H@localhost whoami;
}{% endhighlight %}

Run script

{% highlight shell %}$ bash sshPublicTest.sh 
soivite01
soivite02
soivite03
soivite04{% endhighlight %}

This post is part of [course](http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013)