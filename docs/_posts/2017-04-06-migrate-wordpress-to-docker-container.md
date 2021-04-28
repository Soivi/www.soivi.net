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
![migrate WordPress to Docker](/assets/2017/04/docker-min.png)

This is the tutorial how to migrate WordPress to a Docker container. I have [LAMP](https://soivi.net/2014/how-to-install-lamp/) on my server and I'm going to use [LEMP](https://en.wikipedia.org/wiki/Solution_stack) on my Docker container.

If you need more ideas on how to make your Docker environment more complex, in following link you can take a look how you can use a proxy, a logging software, LAMP and LEMP on same the enviroment: [docker-server-stack](https://github.com/Soivi/docker-server-stack). I prefer you to start with migrating WordPress to Docker container and after that make it more complex.

## Mysqldump and tar

First find out what is your WordPress database name (wpdb), user (wpuser) and password (wppassword). You can find it in your wp-config.php file in your server

{% highlight shell %}
$ cat wordpress/wp-config.php

...
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'wpdb');

/** MySQL database username */
define('DB_USER', 'wpuser');

/** MySQL database password */
define('DB_PASSWORD', 'wppassword');
...
{% endhighlight %}

Now you know your wpuser, wppassword and wpdb. Use this information to create a mysqldump that contains your Wordpress database

{% highlight shell %}
$ mysqldump -u [wpuser] -p[wppassword] [wpdb] | gzip -9 > wordpress.sql.gz
{% endhighlight %}

Create a tar file from your WordPress folder

{% highlight shell %}
$ tar -zcvf wordpress.tar.gz wordpress/
{% endhighlight %}

## A docker LEMP stack

I prefer to use [LEMP stack](https://github.com/Soivi/docker-lemp-stack) than Dockers own WordPress container. This is because this way it is easier to control every single component and if necessary to debug something. Of course you can use [WordPress container](https://hub.docker.com/_/wordpress/) too.

Clone git repository and move your mysqldump and Wordpress tar to repository.

{% highlight shell %}
$ git clone https://github.com/Soivi/docker-lemp-stack
$ mv wordpress.tar.gz docker-lemp-stack/
$ mv wordpress.sql.gz docker-lemp-stack/
$ cd docker-lemp-stack
{% endhighlight %}

Configure docker-compose file

{% highlight shell %}
$ nano docker-compose.lemp.yaml
{% endhighlight %}

Change your wpdb, wpuser and wppassword. These needs to be same as in the wp-config.php. MYSQL_ROOT_PASSWORD can be whatever you want. Of course I recommend it should be secure password.

{% highlight shell %}
      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=[wpdb]
      - MYSQL_USER=[wpuser]
      - MYSQL_PASSWORD=[wppassword]
{% endhighlight %}

On the docker file change your nginx and fpm volumes to wordpress

{% highlight shell %}
    volumes:
      - ./www/wordpress:/var/www/wordpress
{% endhighlight %}

I recommend to add volume to MySQL. This way it is easy to move your whole stack to other enviroment, backup your data and databases aren't inside of Docker container.

{% highlight shell %}
    volumes:
      - ./data/mysql:/var/lib/mysql
{% endhighlight %}

Now your docker-compose.lemp.yaml should look something like this

{% highlight shell %}
version: '2'
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
{% endhighlight %}

If you created volume to mysql. Create data folder.

{% highlight shell %}
$ mkdir data
{% endhighlight %}

Change tester folder to wordpress. So it matches your volumes in docker-compose file

{% highlight shell %}
$ mv www/tester www/wordpress
{% endhighlight %}

Change in dbtest.php your wpuser, wppassword and wpdb. This way you can test your LEMP stack.

{% highlight shell %}
$ nano www/wordpress/dbtest.php

        // Create connection
        $con=mysqli_connect("mysql","[wpuser]","[wppassword]","[wpdb]");
{% endhighlight %}

In conf.d you need to change your server_name to your domain and root directory to /var/www/wordpress

{% highlight shell %}
$ nano configures/conf.d/default.conf

...
    server_name [domain];
    root /var/www/wordpress;
...
{% endhighlight %}

You need to do this step if you want to test your WordPress on local machine.

{% highlight shell %}
$ sudoedit /etc/hosts

127.0.0.1 [domain]
{% endhighlight %}

## Testing your LEMP stack

Start your LEMP stack (this could take a minute)

{% highlight shell %}
$ sudo docker-compose -f docker-compose.lemp.yaml up -d
{% endhighlight %}

Now curl should answer from index.html. Nginx is working and answering.

{% highlight shell %}
$ curl [domain]/index.html

<!DOCTYPE HTML>
<html>
    <body>
         <p>Hello World!</p>
    </body>
</html>
{% endhighlight %}

Your index.php should be answering "My first PHP page". Now you know your fpm is working.

{% highlight shell %}
$ curl [domain]/index.php

<!DOCTYPE html>
<html>
    <body>
        <h1>My first PHP page</h1>
        <p>Hello World!</p>
    </body>
</html>
{% endhighlight %}

Finally curl dbtest.php. Answer should be just: works. Now you know MySQL is working.

{% highlight shell %}
$ curl [domain]/dbtest.php

works
{% endhighlight %}

So now your LEMP stack is fully working. If something went wrong don't go further. You need to have LEMP stack working before continuing. Go back and recheck every step. You can easily start debugging that specific component that didn't work. Probably you just typed something wrong or forgot something.

## Adding mysqldump to docker

First you need mysql-client if some reason you don't have. Like if you are building fresh environment.

{% highlight shell %}
$ sudo apt install mysql-client
{% endhighlight %}

Then you need to know what IP address your Docker MySQL is running. Give this command to find out IP address

{% highlight shell %}
$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' dockerlempstack_mysql_1

172.20.0.2
{% endhighlight %}

So my Docker MySQL running on 172.20.0.2\. So this is mysql_ip. Then restore your mysqldump to Docker Mysql.

{% highlight shell %}
$ gunzip < wordpress.sql.gz | mysql -u [wpuser] -p -h [mysql_ip] [wpdb]
{% endhighlight %}

After that you can stop your LEMP stack

{% highlight shell %}
$ sudo docker-compose -f docker-compose.lemp.yaml stop
{% endhighlight %}

## WordPress folder to Docker

Then remove your testing folder

{% highlight shell %}
$ rm -r www/wordpress/
{% endhighlight %}

Unpack your Wordpress folder from tar to www folder

{% highlight shell %}
$ tar -vzxf wordpress.tar.gz -C www/
{% endhighlight %}

Now you need to configure in wp-config.php your DB_HOST correctly. DB_HOST is the name of the mysql container on compose file so it is just mysql.

{% highlight shell %}
$ nano www/wordpress/wp-config.php

...
define('DB_HOST', 'mysql');
...
{% endhighlight %}

I prefer to add correct ownership and rights to WordPress folder.

{% highlight shell %}
$ sudo chown -R 33:33 www/wordpress/
$ sudo chmod -R 755 www/wordpress/
$ sudo chmod 444 www/wordpress/wp-config.php
{% endhighlight %}

Now you can start your LEMP stack

{% highlight shell %}
$ sudo docker-compose -f docker-compose.lemp.yaml up -d
{% endhighlight %}

#### Now you have migrate WordPress to Docker

If you want to configure more of your Docker environment you can take a look how it is done:  
[docker-server-stack](https://github.com/Soivi/docker-server-stack)

### Useful commands

Stop docker-compose file containers

{% highlight shell %}
$ sudo docker-compose -f <filename>.yaml stop</filename>
{% endhighlight %}

Delete docker-compose file containers and volumes

{% highlight shell %}
$ sudo docker-compose -f <filename>.yaml rm -v</filename>
{% endhighlight %}

Stop all containers

{% highlight shell %}
$ sudo docker stop $(sudo docker ps -a -q)
{% endhighlight %}

Delete all containers and volumes

{% highlight shell %}
$ sudo docker rm -v $(sudo docker ps -a -q)
{% endhighlight %}

Delete all images

{% highlight shell %}
$ sudo docker rmi $(sudo docker images -q)
{% endhighlight %}

Comment and let me know what you think of this guide. Did you migrate WordPress to Docker correctly?