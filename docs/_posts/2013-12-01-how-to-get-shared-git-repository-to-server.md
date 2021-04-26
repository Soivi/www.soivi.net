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
<p>This tutorial<br />
- I'm creating shared folder that is easy to share with other users<br />
- I'm creating Git repository to local and move it to server so other users can us it as well.</p>
<p>I’m using Xubuntu 12.04.03 32bit<br />
You need to have installed openssh-server and git.</p>
<p>Create user and lock it.</p>
<pre>server$ sudo adduser soivishare
server$ sudo usermod --lock soivishare</pre>
<p>Create folder and give group rights. Use SetGID ( 's' ) so all folders have same permissions.</p>
<pre>server$ sudo mkdir /home/soivishare/repository
server$ sudo chmod g+rwxs /home/soivishare/repository/</pre>
<p>Add folder owner group to soivishare and add it to users what can use the shared folder.</p>
<pre>server$ sudo chown .soivishare /home/soivishare/repository/
server$ sudo adduser exampleUser soivishare</pre>
<p>You need to logout / exit to get rights working.</p>
<pre>server$ exit</pre>
<p>Login again and create file.</p>
<pre>server$ nano /home/soivishare/repository/hello.txt
server$ cat /home/soivishare/repository/hello.txt 
Hello World!</pre>
<p>Now you have shared folder.</p>
<p>Create repository</p>
<pre>server$ cd /home/soivishare/repository/
server$ mkdir helloGit.git
server$ cd helloGit.git/
server$ git init --bare --shared
server$ exit</pre>
<p>Create folder what you want to add in repository.</p>
<pre>$ mkdir helloGit
$ cd helloGit/</pre>
<p>Create empty git and add README file</p>
<pre>$ git init
$ nano README

Hello GIT</pre>
<p>Add and commit. Using -m you can write message directly to commit command.</p>
<pre>$ git add .
$ git commit -m "Added README"</pre>
<p>Add repository to your repository</p>
<pre>$ git remote add origin ssh://exampleUser@example.net/home/soivishare/repository/helloGit.git</pre>
<p>Push your project to repository</p>
<pre>$ git push origin master
$ git branch --set-upstream master origin/master</pre>
<p>Modify README and test repository really works</p>
<pre>$ nano README
MODIFIED 27.11.2013
Hello GIT
$ git add . && git commit -m "Modified README"
$ git pull && git push
$ cd ..</pre>
<p>Create new folder and make clone.</p>
<pre>$ mkdir clone
$ cd clone/
$ git clone ssh://exampleUser@example.net/home/soivishare/repository/helloGit.git</pre>
<p>Test that your newest version comes to repository.</p>
<pre>$ cat helloGit/README 
MODIFIED 27.11.2013
Hello GIT</pre>
<p>Now you have:<br />
- Shared folder that other users can use<br />
- Local repository<br />
- Server repository</p>
<p>Source<br />
<a href="http://samuelkontiomaa.com/2013/11/30/initializing-git-remote-server/">Initializing Git remote server</a><br />
<a href="http://terokarvinen.com/2012/git-from-offline-to-network">Git from Offline to Network</a><br />
<a href="http://terokarvinen.com/2011/shared-folder-with-chmod-setgid">Shared Folder with chmod SetGID</a></p>
<p>This post is part of <a href="http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013">course</a></p>