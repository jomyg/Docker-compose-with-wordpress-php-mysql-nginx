# Docker-compose with  Docker-compose-with-wordpres-php-mysql-nginx

[![Build](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

---

## Description

WordPress is a free and open-source Content Management System (CMS) built on a MySQL database with PHP processing. Thanks to its extensible plugin architecture and templating system, and the fact that most of its administration can be done through the web interface, WordPress is a popular choice when creating different types of websites, from blogs to product pages to eCommerce sites.

Running WordPress typically involves installing a LAMP (Linux, Apache, MySQL, and PHP) or LEMP (Linux, Nginx, MySQL, and PHP) stack, which can be time-consuming. However, by using tools like Docker and Docker Compose, you can simplify the process of setting up your preferred stack and installing WordPress. Instead of installing individual components by hand, you can use images, which standardize things like libraries, configuration files, and environment variables, and run these images in containers, isolated processes that run on a shared operating system.

----
## Pre-Requests
- Need to install docker and docker-compose
-----

## Includes

> Containers we are creating (MySQL, WordPress+PHP-FPM, Nginx)
> Volumes (For MySQL and WordPress)
> Network 

### Docker installation 

```sh
yum install docker -y after docker installation, please start and enable it
```
### docker-compose installation

```sh
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose
sudo chmod +x /usr/bin/docker-compose
docker-compose version   
```

### Behind the code

> Now we can create a file called "docker-compose.yml"
```sh
version: '3'

services:

  database:

    image: mysql:5.7
    container_name: mysql
    restart: always
    networks:
      - wp-net
    volumes:
      - mysql-volume:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: mysqlroot123
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:

    image: wordpress:php7.4-fpm-alpine
    container_name: wordpress
    restart: always
    networks:
      - wp-net
    volumes:
      - wp-volume:/var/www/html
    environment:
      WORDPRESS_DB_HOST: database
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress


  nginx:

    image: nginx:alpine
    container_name: nginx
    restart: always
    ports:
      - "80:80"
      - "443:443"
    networks:
      - wp-net
    volumes:
      - wp-volume:/var/www/html
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
      - ./jomygeorge.xyz.crt:/var/ssl/jomygeorge.xyz.crt
      - ./jomygeorge.xyz.key:/var/ssl/jomygeorge.xyz.key


volumes:
  mysql-volume:
  wp-volume:

networks:
  wp-net:
  ```
 ### Nginx reverse proxy configuration file for the nginx container creation, To do this, I have created a file called nginx.conf
 
 > I have used openssl to generate the SSL certificate for this setup
 
  ```sh
  server {
    listen 80;
    return 301 https://jomygeorge.xyz;
}
server {
#   listen 80;
    listen 443 ssl;

    server_name jomygeorge.xyz;

    ssl_certificate /var/ssl/jomygeorge.xyz.crt;
    ssl_certificate_key  /var/ssl/jomygeorge.xyz.key;

    root /var/www/html;
    index index.php;

    if ($request_method = POST) {
        set $cache_uri 'null cache';
    }
    if ($query_string != "") {
        set $cache_uri 'null cache';
    }

   location / {
    try_files $uri $uri/ /index.php?$args;
  }

#PHP scripts to FastCGI server listening on wordpress:9000
  location ~ \.php$ {
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass wordpress:9000;
    include fastcgi_params;
    fastcgi_index index.php ;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_param SCRIPT_NAME $fastcgi_script_name;
  }
}
```
> After all file created
```
project]# ls -l
total 16
-rw-r--r-- 1 root root 1128 Feb 17 12:18 docker-compose.yml
-rw-r--r-- 1 root root 1220 Feb 17 11:02 jomygeorge.xyz.crt
-rw-r--r-- 1 root root 1708 Feb 17 11:02 jomygeorge.xyz.key
-rw-r--r-- 1 root root  830 Feb 17 12:24 nginx.conf
```
### How to run the compose

> docker-compose config              # To check the syntax

> RUn docker-compose up -d           #--> This will create all the requirement which we have added on the yml file


> docker-compose ps   

              
> docker network ls                  # To view the networks created


> docker volume ls                   # To view the volumes created


> docker-compose down                # To remove all created container and resources


> You need to remove volumes manualy 


## After creation
```
project]# docker container ls
CONTAINER ID   IMAGE                         COMMAND                  CREATED         STATUS         PORTS                                                                      NAMES
6f1613d7b3b5   mysql:5.7                     "docker-entrypoint.s…"   7 minutes ago   Up 7 minutes   3306/tcp, 33060/tcp                                                        mysql
e639f82c6b6b   wordpress:php7.4-fpm-alpine   "docker-entrypoint.s…"   7 minutes ago   Up 7 minutes   9000/tcp                                                                   wordpress
942fe62d1a0d   nginx:alpine                  "/docker-entrypoint.…"   7 minutes ago   Up 7 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   nginx

project]# docker image ls
REPOSITORY   TAG                 IMAGE ID       CREATED       SIZE
wordpress    php7.4-fpm-alpine   4dd3e804c85a   2 weeks ago   307MB
mysql        5.7                 0712d5dc1b14   3 weeks ago   448MB
nginx        alpine              bef258acf10d   3 weeks ago   23.4MB

project]# docker volume ls
DRIVER    VOLUME NAME
local     project_mysql-volume
local     project_wp-volume
project]#
```
> Commmand to create the SSL cert
 ```sh
 openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout example.com.key -out example.com.crt
 ```
 
 ## Conclusion

Created the wordpress with php, Nginx and mysql using docker compose


#### ⚙️ Connect with Me

<p align="center">
<a href="mailto:jomyambattil@gmail.com"><img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white"/></a>
<a href="https://www.linkedin.com/in/jomygeorge11"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white"/></a> 
<a href="https://www.instagram.com/therealjomy"><img src="https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white"/></a><br />
