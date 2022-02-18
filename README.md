# Docker-compose with  Docker-compose-with-wordpres-php-mysql-nginx

[![Build](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

---

## Description


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
