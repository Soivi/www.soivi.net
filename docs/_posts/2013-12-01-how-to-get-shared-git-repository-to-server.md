---
layout: post
title: How to get shared Git repository to server
date: 2013-12-01 18:07:00.000000000 +02:00
categories:
- Linux
- Linux centralized management
tags:
- Debian
- Git
- Linux
- Repository
- Server
- SetGID
- Shared folder
- Ubuntu
- Ubuntu 12.04
- Xubuntu
- Xubuntu 12.04
permalink: "/2013/how-to-get-shared-git-repository-to-server/"
---
This tutorial  
- I'm creating shared folder that is easy to share with other users  
- I'm creating Git repository to local and move it to server so other users can us it as well.

I’m using Xubuntu 12.04.03 32bit  
You need to have installed openssh-server and git.

Create user and lock it.

{% highlight shell %}
server$ sudo adduser soivishare
server$ sudo usermod --lock soivishare
{% endhighlight %}

Create folder and give group rights. Use SetGID ( 's' ) so all folders have same permissions.

{% highlight shell %}
server$ sudo mkdir /home/soivishare/repository
server$ sudo chmod g+rwxs /home/soivishare/repository/
{% endhighlight %}

Add folder owner group to soivishare and add it to users what can use the shared folder.

{% highlight shell %}
server$ sudo chown .soivishare /home/soivishare/repository/
server$ sudo adduser exampleUser soivishare
{% endhighlight %}

You need to logout / exit to get rights working.

{% highlight shell %}
server$ exit
{% endhighlight %}

Login again and create file.

{% highlight shell %}
server$ nano /home/soivishare/repository/hello.txt
server$ cat /home/soivishare/repository/hello.txt 
Hello World!
{% endhighlight %}

Now you have shared folder.

Create repository

{% highlight shell %}
server$ cd /home/soivishare/repository/
server$ mkdir helloGit.git
server$ cd helloGit.git/
server$ git init --bare --shared
server$ exit
{% endhighlight %}

Create folder what you want to add in repository.

{% highlight shell %}
$ mkdir helloGit
$ cd helloGit/
{% endhighlight %}

Create empty git and add README file

{% highlight shell %}
$ git init
$ nano README

Hello GIT
{% endhighlight %}

Add and commit. Using -m you can write message directly to commit command.

{% highlight shell %}
$ git add .
$ git commit -m "Added README"
{% endhighlight %}

Add repository to your repository

{% highlight shell %}
$ git remote add origin ssh://exampleUser@example.net/home/soivishare/repository/helloGit.git
{% endhighlight %}

Push your project to repository

{% highlight shell %}
$ git push origin master
$ git branch --set-upstream master origin/master
{% endhighlight %}

Modify README and test repository really works

{% highlight shell %}
$ nano README
MODIFIED 27.11.2013
Hello GIT
$ git add . && git commit -m "Modified README"
$ git pull && git push
$ cd ..
{% endhighlight %}

Create new folder and make clone.

{% highlight shell %}
$ mkdir clone
$ cd clone/
$ git clone ssh://exampleUser@example.net/home/soivishare/repository/helloGit.git
{% endhighlight %}

Test that your newest version comes to repository.

{% highlight shell %}
$ cat helloGit/README 
MODIFIED 27.11.2013
Hello GIT
{% endhighlight %}

Now you have:  
- Shared folder that other users can use  
- Local repository  
- Server repository

Source  
[Initializing Git remote server](http://samuelkontiomaa.com/2013/11/30/initializing-git-remote-server/)  
[Git from Offline to Network](http://terokarvinen.com/2012/git-from-offline-to-network)  
[Shared Folder with chmod SetGID](http://terokarvinen.com/2011/shared-folder-with-chmod-setgid)

This post is part of [course](http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013)