---
layout: post
title: How to migrate WordPress to a Docker container
date: 2017-04-06 20:45:09.000000000 +03:00
categories:
- Linux
- Linux as a server
- Wordpress
tags:
- Apache
- Docker
- Docker Compose
- LAMP
- LEMP
- Linux
- MySQL
- Ubuntu
- Wordpress
permalink: "/2017/migrate-wordpress-to-docker-container/"
---
<p><a href="https://www.docker.com/"><img src="{{ site.baseurl }}/assets/2017/04/docker-min.png" alt="migrate WordPress to Docker" width="169" height="151" class="alignright size-full wp-image-1163" /></a></p>
<p>This is the tutorial how to migrate WordPress to a Docker container. I have <a href="https://soivi.net/2014/how-to-install-lamp/" target="_blank" rel="noopener noreferrer">LAMP</a> on my server and I'm going to use <a href="https://en.wikipedia.org/wiki/Solution_stack" target="_blank" rel="noopener noreferrer">LEMP</a> on my Docker container.</p>
<p>If you need more ideas on how to make your Docker environment more complex, in following link you can take a look how you can use a proxy, a logging software, LAMP and LEMP on same the enviroment: <a href="https://github.com/Soivi/docker-server-stack">docker-server-stack</a>. I prefer you to start with migrating WordPress to Docker container and after that make it more complex.</p>
<h2>Mysqldump and tar</h2>
<p>First find out what is your WordPress database name (wpdb), user (wpuser) and password (wppassword). You can find it in your wp-config.php file in your server</p>
<pre>$ cat wordpress/wp-config.php

...
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'wpdb');

/** MySQL database username */
define('DB_USER', 'wpuser');

/** MySQL database password */
define('DB_PASSWORD', 'wppassword');
...
</pre>
<p>Now you know your wpuser, wppassword and wpdb. Use this information to create a mysqldump that contains your Wordpress database</p>
<pre>$ mysqldump -u [wpuser] -p[wppassword] [wpdb] | gzip -9 &gt; wordpress.sql.gz
</pre>
<p>Create a tar file from your WordPress folder</p>
<pre>$ tar -zcvf wordpress.tar.gz wordpress/
</pre>
<h2>A docker LEMP stack</h2>
<p>I prefer to use <a href="https://github.com/Soivi/docker-lemp-stack">LEMP stack</a> than Dockers own WordPress container. This is because this way it is easier to control every single component and if necessary to debug something. Of course you can use <a href="https://hub.docker.com/_/wordpress/">WordPress container</a> too.</p>
<p>Clone git repository and move your mysqldump and Wordpress tar to repository.</p>
<pre>$ git clone https://github.com/Soivi/docker-lemp-stack
$ mv wordpress.tar.gz docker-lemp-stack/
$ mv wordpress.sql.gz docker-lemp-stack/
$ cd docker-lemp-stack
</pre>
<p>Configure docker-compose file</p>
<pre>$ nano docker-compose.lemp.yaml
</pre>
<p>Change your wpdb, wpuser and wppassword. These needs to be same as in the wp-config.php. MYSQL_ROOT_PASSWORD can be whatever you want. Of course I recommend it should be secure password.</p>
<pre>      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=[wpdb]
      - MYSQL_USER=[wpuser]
      - MYSQL_PASSWORD=[wppassword]
</pre>
<p>On the docker file change your nginx and fpm volumes to wordpress</p>
<pre>    volumes:
      - ./www/wordpress:/var/www/wordpress
</pre>
<p>I recommend to add volume to MySQL. This way it is easy to move your whole stack to other enviroment, backup your data and databases aren't inside of Docker container.</p>
<pre>    volumes:
      - ./data/mysql:/var/lib/mysql
</pre>
<p>Now your docker-compose.lemp.yaml should look something like this</p>
<pre>version: '2'
services:
  nginx:
    image: nginx:1.11
    ports:
      - "80:80"
    links:
      - mysql
      - fpm
    volumes:
      - ./www/wordpress:/var/www/wordpress
      - ./configures/conf.d:/etc/nginx/conf.d:ro

  mysql:
    image: mysql:8.0
    environment:
      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=[wpdb]
      - MYSQL_USER=[wpuser]
      - MYSQL_PASSWORD=[wppassword]
    volumes:
      - ./data/mysql:/var/lib/mysql

  fpm:
    build: images/nginxphp
    volumes:
      - ./www/wordpress:/var/www/wordpress
    links:
      - mysql
</pre>
<p>If you created volume to mysql. Create data folder.</p>
<pre>$ mkdir data
</pre>
<p>Change tester folder to wordpress. So it matches your volumes in docker-compose file</p>
<pre>$ mv www/tester www/wordpress
</pre>
<p>Change in dbtest.php your wpuser, wppassword and wpdb. This way you can test your LEMP stack.</p>
<pre>$ nano www/wordpress/dbtest.php

        // Create connection
        $con=mysqli_connect("mysql","[wpuser]","[wppassword]","[wpdb]");
</pre>
<p>In conf.d you need to change your server_name to your domain and root directory to /var/www/wordpress</p>
<pre>$ nano configures/conf.d/default.conf

...
    server_name [domain];
    root /var/www/wordpress;
...
</pre>
<p>You need to do this step if you want to test your WordPress on local machine.</p>
<pre>$ sudoedit /etc/hosts

127.0.0.1 [domain]
</pre>
<h2>Testing your LEMP stack</h2>
<p>Start your LEMP stack (this could take a minute)</p>
<pre>$ sudo docker-compose -f docker-compose.lemp.yaml up -d
</pre>
<p>Now curl should answer from index.html. Nginx is working and answering.</p>
<pre>$ curl [domain]/index.html

&lt;!DOCTYPE HTML&gt;
&lt;html&gt;
    &lt;body&gt;
         &lt;p&gt;Hello World!&lt;/p&gt;
    &lt;/body&gt;
&lt;/html&gt;
</pre>
<p>Your index.php should be answering "My first PHP page". Now you know your fpm is working.</p>
<pre>$ curl [domain]/index.php

&lt;!DOCTYPE html&gt;
&lt;html&gt;
    &lt;body&gt;
        &lt;h1&gt;My first PHP page&lt;/h1&gt;
        &lt;p&gt;Hello World!&lt;/p&gt;
    &lt;/body&gt;
&lt;/html&gt;
</pre>
<p>Finally curl dbtest.php. Answer should be just: works. Now you know MySQL is working.</p>
<pre>$ curl [domain]/dbtest.php

works
</pre>
<p>So now your LEMP stack is fully working. If something went wrong don't go further. You need to have LEMP stack working before continuing. Go back and recheck every step. You can easily start debugging that specific component that didn't work. Probably you just typed something wrong or forgot something.</p>
<h2>Adding mysqldump to docker</h2>
<p>First you need mysql-client if some reason you don't have. Like if you are building fresh environment.</p>
<pre>$ sudo apt install mysql-client
</pre>
<p>Then you need to know what IP address your Docker MySQL is running. Give this command to find out IP address</p>
<pre>$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' dockerlempstack_mysql_1

172.20.0.2
</pre>
<p>So my Docker MySQL running on 172.20.0.2. So this is mysql_ip. Then restore your mysqldump to Docker Mysql.</p>
<pre>$ gunzip &lt; wordpress.sql.gz | mysql -u [wpuser] -p -h [mysql_ip] [wpdb]
</pre>
<p>After that you can stop your LEMP stack</p>
<pre>$ sudo docker-compose -f docker-compose.lemp.yaml stop
</pre>
<h2>WordPress folder to Docker</h2>
<p>Then remove your testing folder</p>
<pre>$ rm -r www/wordpress/
</pre>
<p>Unpack your Wordpress folder from tar to www folder</p>
<pre>$ tar -vzxf wordpress.tar.gz -C www/
</pre>
<p>Now you need to configure in wp-config.php your DB_HOST correctly. DB_HOST is the name of the mysql container on compose file so it is just mysql.</p>
<pre>$ nano www/wordpress/wp-config.php

...
define('DB_HOST', 'mysql');
...
</pre>
<p>I prefer to add correct ownership and rights to WordPress folder.</p>
<pre>$ sudo chown -R 33:33 www/wordpress/
$ sudo chmod -R 755 www/wordpress/
$ sudo chmod 444 www/wordpress/wp-config.php
</pre>
<p>Now you can start your LEMP stack</p>
<pre>$ sudo docker-compose -f docker-compose.lemp.yaml up -d
</pre>
<h4>Now you have migrate WordPress to Docker</h4>
<p>If you want to configure more of your Docker environment you can take a look how it is done:<br />
<a href="https://github.com/Soivi/docker-server-stack">docker-server-stack</a></p>
<h3>Useful commands</h3>
<p>Stop docker-compose file containers</p>
<pre>$ sudo docker-compose -f <filename>.yaml stop
</filename></pre>
<p>Delete docker-compose file containers and volumes</p>
<pre>$ sudo docker-compose -f <filename>.yaml rm -v
</filename></pre>
<p>Stop all containers</p>
<pre>$ sudo docker stop $(sudo docker ps -a -q)
</pre>
<p>Delete all containers and volumes</p>
<pre>$ sudo docker rm -v $(sudo docker ps -a -q)
</pre>
<p>Delete all images</p>
<pre>$ sudo docker rmi $(sudo docker images -q)
</pre>
<p>Comment and let me know what you think of this guide. Did you migrate WordPress to Docker correctly?</p>
