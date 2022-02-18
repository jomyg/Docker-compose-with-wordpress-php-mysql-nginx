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
