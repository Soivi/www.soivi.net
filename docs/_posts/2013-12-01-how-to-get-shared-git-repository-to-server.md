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

<pre>server$ sudo adduser soivishare
server$ sudo usermod --lock soivishare</pre>

Create folder and give group rights. Use SetGID ( 's' ) so all folders have same permissions.

<pre>server$ sudo mkdir /home/soivishare/repository
server$ sudo chmod g+rwxs /home/soivishare/repository/</pre>

Add folder owner group to soivishare and add it to users what can use the shared folder.

<pre>server$ sudo chown .soivishare /home/soivishare/repository/
server$ sudo adduser exampleUser soivishare</pre>

You need to logout / exit to get rights working.

<pre>server$ exit</pre>

Login again and create file.

<pre>server$ nano /home/soivishare/repository/hello.txt
server$ cat /home/soivishare/repository/hello.txt 
Hello World!</pre>

Now you have shared folder.

Create repository

<pre>server$ cd /home/soivishare/repository/
server$ mkdir helloGit.git
server$ cd helloGit.git/
server$ git init --bare --shared
server$ exit</pre>

Create folder what you want to add in repository.

<pre>$ mkdir helloGit
$ cd helloGit/</pre>

Create empty git and add README file

<pre>$ git init
$ nano README

Hello GIT</pre>

Add and commit. Using -m you can write message directly to commit command.

<pre>$ git add .
$ git commit -m "Added README"</pre>

Add repository to your repository

<pre>$ git remote add origin ssh://exampleUser@example.net/home/soivishare/repository/helloGit.git</pre>

Push your project to repository

<pre>$ git push origin master
$ git branch --set-upstream master origin/master</pre>

Modify README and test repository really works

<pre>$ nano README
MODIFIED 27.11.2013
Hello GIT
$ git add . && git commit -m "Modified README"
$ git pull && git push
$ cd ..</pre>

Create new folder and make clone.

<pre>$ mkdir clone
$ cd clone/
$ git clone ssh://exampleUser@example.net/home/soivishare/repository/helloGit.git</pre>

Test that your newest version comes to repository.

<pre>$ cat helloGit/README 
MODIFIED 27.11.2013
Hello GIT</pre>

Now you have:  
- Shared folder that other users can use  
- Local repository  
- Server repository

Source  
[Initializing Git remote server](http://samuelkontiomaa.com/2013/11/30/initializing-git-remote-server/)  
[Git from Offline to Network](http://terokarvinen.com/2012/git-from-offline-to-network)  
[Shared Folder with chmod SetGID](http://terokarvinen.com/2011/shared-folder-with-chmod-setgid)

This post is part of [course](http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013)