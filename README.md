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
yum install docker -y
yum install git -y
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
