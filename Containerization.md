![Running containers](https://devops-final-project-jc.s3.us-east-1.amazonaws.com/Screenshot+2026-02-22+204619.png)
~
# Containerization Quick Reference

## Install Docker (Red Hat / EC2)

```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ec2-user
```

*Log out and back in after `usermod` for the group change to take effect.*

---

## Core Docker Commands

```bash
docker pull mysql:latest
docker pull mysql:8.0
docker ps                          # list running containers
docker ps -a                       # list all containers including stopped
docker stop mysql-server
docker rm mysql-server
docker container inspect mysql-server
```

---

## Running Containers

```bash
# MySQL with environment variable
docker run --name mysql-server -e MYSQL_ROOT_PASSWORD=student99 -d mysql:latest

# MySQL with persistent volume
docker run --name mysql-server -e MYSQL_ROOT_PASSWORD=student99 -v mysql-data:/var/lib/mysql -d mysql:latest

# nginx with port mapping and volume
docker run -d -p 8080:80 --name nginx-server -v nginx-data:/usr/share/nginx/html nginx

# php-nginx (note different path and port)
docker run -d -p 8080:8080 --name nginx-server -v nginx-data:/var/www/html:rw trafex/php-nginx
```

---

## Volumes

```bash
docker volume create mysql-data
docker volume rm nginx-data
```

---

## Exec Into a Container

```bash
docker exec -it mysql-server mysql -uroot -p    # open MySQL session
docker exec -it nginx-server bash               # bash shell
docker exec -it nginx-server sh                 # sh shell (lightweight containers)
```

---

## SQL Commands (Inside MySQL Container)

```bash
show databases;
use mysql;
show tables;
select * from tables_priv;
create database wordpress;
create database cms;
exit;
```

---

## Docker Compose

```bash
docker compose up -d        # start all services in background
docker compose down         # stop and remove all containers and networks
```

---

## docker-compose.yml — WordPress + MySQL + phpMyAdmin

```yaml
services:
  mysql:
    image: mysql:8.0
    container_name: mysql
    environment:
      - MYSQL_ROOT_PASSWORD=student99
      - MYSQL_ROOT_HOST=%
      - MYSQL_DATABASE=wordpress
      - MYSQL_USER=wpuser
      - MYSQL_PASSWORD=wpPassword123!
    networks:
      - wordpress_network
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    container_name: wordpress
    depends_on:
      - mysql
    ports:
      - "80:80"
    environment:
      - WORDPRESS_DB_HOST=mysql:3306
      - WORDPRESS_DB_USER=wpuser
      - WORDPRESS_DB_PASSWORD=wpPassword123!
      - WORDPRESS_DB_NAME=wordpress
    networks:
      - wordpress_network
    volumes:
      - wordpress_data:/var/www/html

  phpmyadmin:
    image: phpmyadmin:latest
    container_name: phpmyadmin
    depends_on:
      - mysql
    ports:
      - "81:80"
    environment:
      - PMA_HOST=mysql
      - PMA_PORT=3306
    networks:
      - wordpress_network

networks:
  wordpress_network:
    driver: bridge

volumes:
  wordpress_data:
    driver: local
  db_data:
    driver: local
```

---

## docker-compose.yml — Final Project (Docs + MySQL + Joomla)

```yaml
services:
  documentation:
    image: kobucom/pandoc:local1
    container_name: doc-final-project
    ports:
      - "8081:80"
    volumes:
      - /path/to/your/repo:/usr/local/apache2/htdocs
    networks:
      - final-network
    restart: unless-stopped

  database:
    image: mysql:8.0
    container_name: database-final-project
    environment:
      - MYSQL_ROOT_PASSWORD=ComplexPass123!!
      - MYSQL_DATABASE=cms
    volumes:
      - mysql-final-data:/var/lib/mysql
    networks:
      - final-network
    restart: unless-stopped

  cms:
    image: joomla:latest
    container_name: joomla-final-project
    ports:
      - "8090:80"
    environment:
      - JOOMLA_DB_HOST=database
      - JOOMLA_DB_USER=root
      - JOOMLA_DB_PASSWORD=ComplexPass123!!
      - JOOMLA_DB_NAME=cms
    volumes:
      - web-final-data:/var/www/html
    depends_on:
      - database
    networks:
      - final-network
    restart: unless-stopped

networks:
  final-network:
    driver: bridge

volumes:
  mysql-final-data:
    driver: local
  web-final-data:
    driver: local
```

*Replace `/path/to/your/repo` with the actual path from `pwd` in your cloned repo directory.*

---

## Joomla Database Settings (Installation Wizard)

| Setting | Value |
|---|---|
| Database Type | MySQLi |
| Database Host | `database` |
| Database User | `root` |
| Database Password | value set in docker-compose.yml |
| Database Name | `cms` |

---

## Data Persistence — Testing a Volume

```bash
docker exec -it mysql-server mysql -uroot -p
create database labtest;
show databases;
exit;
docker stop mysql-server
docker rm mysql-server
docker run --name mysql-server -e MYSQL_ROOT_PASSWORD=student99 -v mysql-data:/var/lib/mysql -d mysql:latest
docker exec -it mysql-server mysql -uroot -p
show databases;        # labtest should still be here
```

---

## Inspect Container IP Address

```bash
docker container inspect mysql-server
# Look for "IPAddress" under "Networks"
```

---

## Inside a Container — Install and Edit

```bash
apt update
apt install -y vim
cd /usr/share/nginx/html       # nginx default content path
vim index.html                 # i=insert, ESC, :wq to save
```

---

## Download and Extract WordPress (Inside Container)

```bash
wget https://f005.backblazeb2.com/file/webandsoftware-online/devops/docker/wordpress-6.7.1.tar.gz
tar -xzvf wordpress-6.7.1.tar.gz
```
~
~
~
~

