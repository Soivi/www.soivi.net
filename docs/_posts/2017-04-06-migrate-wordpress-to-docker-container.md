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
[![migrate WordPress to Docker]({{ site.baseurl }}/assets/2017/04/docker-min.png)](https://www.docker.com/)

This is the tutorial how to migrate WordPress to a Docker container. I have [LAMP](https://soivi.net/2014/how-to-install-lamp/) on my server and I'm going to use [LEMP](https://en.wikipedia.org/wiki/Solution_stack) on my Docker container.

If you need more ideas on how to make your Docker environment more complex, in following link you can take a look how you can use a proxy, a logging software, LAMP and LEMP on same the enviroment: [docker-server-stack](https://github.com/Soivi/docker-server-stack). I prefer you to start with migrating WordPress to Docker container and after that make it more complex.

## Mysqldump and tar

First find out what is your WordPress database name (wpdb), user (wpuser) and password (wppassword). You can find it in your wp-config.php file in your server

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

Now you know your wpuser, wppassword and wpdb. Use this information to create a mysqldump that contains your Wordpress database

<pre>$ mysqldump -u [wpuser] -p[wppassword] [wpdb] | gzip -9 > wordpress.sql.gz
</pre>

Create a tar file from your WordPress folder

<pre>$ tar -zcvf wordpress.tar.gz wordpress/
</pre>

## A docker LEMP stack

I prefer to use [LEMP stack](https://github.com/Soivi/docker-lemp-stack) than Dockers own WordPress container. This is because this way it is easier to control every single component and if necessary to debug something. Of course you can use [WordPress container](https://hub.docker.com/_/wordpress/) too.

Clone git repository and move your mysqldump and Wordpress tar to repository.

<pre>$ git clone https://github.com/Soivi/docker-lemp-stack
$ mv wordpress.tar.gz docker-lemp-stack/
$ mv wordpress.sql.gz docker-lemp-stack/
$ cd docker-lemp-stack
</pre>

Configure docker-compose file

<pre>$ nano docker-compose.lemp.yaml
</pre>

Change your wpdb, wpuser and wppassword. These needs to be same as in the wp-config.php. MYSQL_ROOT_PASSWORD can be whatever you want. Of course I recommend it should be secure password.

<pre>      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=[wpdb]
      - MYSQL_USER=[wpuser]
      - MYSQL_PASSWORD=[wppassword]
</pre>

On the docker file change your nginx and fpm volumes to wordpress

<pre>    volumes:
      - ./www/wordpress:/var/www/wordpress
</pre>

I recommend to add volume to MySQL. This way it is easy to move your whole stack to other enviroment, backup your data and databases aren't inside of Docker container.

<pre>    volumes:
      - ./data/mysql:/var/lib/mysql
</pre>

Now your docker-compose.lemp.yaml should look something like this

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

If you created volume to mysql. Create data folder.

<pre>$ mkdir data
</pre>

Change tester folder to wordpress. So it matches your volumes in docker-compose file

<pre>$ mv www/tester www/wordpress
</pre>

Change in dbtest.php your wpuser, wppassword and wpdb. This way you can test your LEMP stack.

<pre>$ nano www/wordpress/dbtest.php

        // Create connection
        $con=mysqli_connect("mysql","[wpuser]","[wppassword]","[wpdb]");
</pre>

In conf.d you need to change your server_name to your domain and root directory to /var/www/wordpress

<pre>$ nano configures/conf.d/default.conf

...
    server_name [domain];
    root /var/www/wordpress;
...
</pre>

You need to do this step if you want to test your WordPress on local machine.

<pre>$ sudoedit /etc/hosts

127.0.0.1 [domain]
</pre>

## Testing your LEMP stack

Start your LEMP stack (this could take a minute)

<pre>$ sudo docker-compose -f docker-compose.lemp.yaml up -d
</pre>

Now curl should answer from index.html. Nginx is working and answering.

<pre>$ curl [domain]/index.html

<!DOCTYPE HTML>
<html>
    <body>
         <p>Hello World!</p>
    </body>
</html>
</pre>

Your index.php should be answering "My first PHP page". Now you know your fpm is working.

<pre>$ curl [domain]/index.php

<!DOCTYPE html>
<html>
    <body>
        <h1>My first PHP page</h1>
        <p>Hello World!</p>
    </body>
</html>
</pre>

Finally curl dbtest.php. Answer should be just: works. Now you know MySQL is working.

<pre>$ curl [domain]/dbtest.php

works
</pre>

So now your LEMP stack is fully working. If something went wrong don't go further. You need to have LEMP stack working before continuing. Go back and recheck every step. You can easily start debugging that specific component that didn't work. Probably you just typed something wrong or forgot something.

## Adding mysqldump to docker

First you need mysql-client if some reason you don't have. Like if you are building fresh environment.

<pre>$ sudo apt install mysql-client
</pre>

Then you need to know what IP address your Docker MySQL is running. Give this command to find out IP address

<pre>$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' dockerlempstack_mysql_1

172.20.0.2
</pre>

So my Docker MySQL running on 172.20.0.2\. So this is mysql_ip. Then restore your mysqldump to Docker Mysql.

<pre>$ gunzip < wordpress.sql.gz | mysql -u [wpuser] -p -h [mysql_ip] [wpdb]
</pre>

After that you can stop your LEMP stack

<pre>$ sudo docker-compose -f docker-compose.lemp.yaml stop
</pre>

## WordPress folder to Docker

Then remove your testing folder

<pre>$ rm -r www/wordpress/
</pre>

Unpack your Wordpress folder from tar to www folder

<pre>$ tar -vzxf wordpress.tar.gz -C www/
</pre>

Now you need to configure in wp-config.php your DB_HOST correctly. DB_HOST is the name of the mysql container on compose file so it is just mysql.

<pre>$ nano www/wordpress/wp-config.php

...
define('DB_HOST', 'mysql');
...
</pre>

I prefer to add correct ownership and rights to WordPress folder.

<pre>$ sudo chown -R 33:33 www/wordpress/
$ sudo chmod -R 755 www/wordpress/
$ sudo chmod 444 www/wordpress/wp-config.php
</pre>

Now you can start your LEMP stack

<pre>$ sudo docker-compose -f docker-compose.lemp.yaml up -d
</pre>

#### Now you have migrate WordPress to Docker

If you want to configure more of your Docker environment you can take a look how it is done:  
[docker-server-stack](https://github.com/Soivi/docker-server-stack)

### Useful commands

Stop docker-compose file containers

<pre>$ sudo docker-compose -f <filename>.yaml stop</filename> </pre>

Delete docker-compose file containers and volumes

<pre>$ sudo docker-compose -f <filename>.yaml rm -v</filename> </pre>

Stop all containers

<pre>$ sudo docker stop $(sudo docker ps -a -q)
</pre>

Delete all containers and volumes

<pre>$ sudo docker rm -v $(sudo docker ps -a -q)
</pre>

Delete all images

<pre>$ sudo docker rmi $(sudo docker images -q)
</pre>

Comment and let me know what you think of this guide. Did you migrate WordPress to Docker correctly?