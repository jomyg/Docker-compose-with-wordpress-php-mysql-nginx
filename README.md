Creating wordpress site using Docker compose :
    
Intall docker first then install compose
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
docker-compose version 1.29.2, build 5becea4c
[root@ip-172-31-46-156 project]#


[root@ip-172-31-46-156 project]# ls -l
total 16
-rw-r--r-- 1 root root 1128 Feb 17 12:18 docker-compose.yml
-rw-r--r-- 1 root root 1220 Feb 17 11:02 jomygeorge.xyz.crt
-rw-r--r-- 1 root root 1708 Feb 17 11:02 jomygeorge.xyz.key
-rw-r--r-- 1 root root  830 Feb 17 12:24 nginx.conf
    
    
[root@ip-172-31-46-156 project]# cat docker-compose.yml
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

[root@ip-172-31-46-156 project]#
====================================================================================================================
[root@ip-172-31-46-156 project]# cat nginx.conf
server {
    listen 80;
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

You can use below to generate SSL cert
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout jomygeorge.xyz.key -out jomygeorge.xyz.crt
    
Now we call run the project

docker-compose config 
docker-compose up -d

[root@ip-172-31-46-156 project]# docker container ls
CONTAINER ID   IMAGE                         COMMAND                  CREATED         STATUS         PORTS                                                                      NAMES
6f1613d7b3b5   mysql:5.7                     "docker-entrypoint.s…"   7 minutes ago   Up 7 minutes   3306/tcp, 33060/tcp                                                        mysql
e639f82c6b6b   wordpress:php7.4-fpm-alpine   "docker-entrypoint.s…"   7 minutes ago   Up 7 minutes   9000/tcp                                                                   wordpress
942fe62d1a0d   nginx:alpine                  "/docker-entrypoint.…"   7 minutes ago   Up 7 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   nginx
[root@ip-172-31-46-156 project]# docker image ls
REPOSITORY   TAG                 IMAGE ID       CREATED       SIZE
wordpress    php7.4-fpm-alpine   4dd3e804c85a   2 weeks ago   307MB
mysql        5.7                 0712d5dc1b14   3 weeks ago   448MB
nginx        alpine              bef258acf10d   3 weeks ago   23.4MB
[root@ip-172-31-46-156 project]# docker volume ls
DRIVER    VOLUME NAME
local     project_mysql-volume
local     project_wp-volume
[root@ip-172-31-46-156 project]#

You can stop remove container using with in the project directory

docker-compose stop
docker-compose down
