# 1. 配置环境-<span id="config">config</span>

## 1.1 配置 .evn
文件: **.env** [配置示例](#env)

路径: `${YOUR_DOCKER_COMPOSE_ENV_PATH}/.env`

说明：您的docker-compose.yml所需.env的文件所在目录

描述: 运行**deploy-lnmpr-in-docker**的容器服务需要以下参数。

NGINX-参数  | 功能
--      | ----------
 WEB_ROOT_DIR   | 该值为相对路径，该目录将挂载到nginx容器中的`/usr/share/nginx/html/`目录。
 NGINX_VERSION   | 指定nginx版本。
 NGINX_HTTP_HOST_PORT   | 对外映射端口，将映射nginx容器中的`80`端口。
 NGINX_HTTPS_HOST_PORT   | 对外映射端口，将映射nginx容器中的`443`端口。
 NGINX_CONF_DIR   | 该值为相对路径，该目录将挂载到nginx容器中的`/etc/nginx/conf.d/`目录。
 NGINX_CONF_FILE   | 该值为相对路径，该文件将映射到nginx容器中的`/etc/nginx/nginx.conf`文件。
 NGINX_SSL_CERTS_DIR   | 该值为相对路径，该目录将挂载到nginx容器中的`/etc/nginx/certs/`目录。
 NGINX_LOG_DIR   | 值为相对路径，该目录将挂载到nginx容器中的`/var/log/nginx/`目录。

PHP-参数  | 功能
--      | ----------
 PHP_VERSION   | 指定php版本。
 PHP_PHP_CONF_FILE   | 该值为相对路径，该文件将映射到php容器中的`/usr/local/etc/php/php.ini`文件。
 PHP_FPM_CONF_FILE   | 该值为相对路径，该文件将映射到php容器中的`/usr/local/etc/php-fpm.d/www.conf`文件。
 PHP_LOG_DIR   | 该值为相对路径，该目录将挂载到php容器中的`/var/log/`目录。
 PHP_EXTENSIONS   | 该值为php扩展，以空格为分割符。

MYSQL-参数  | 功能
--      | ----------
 MYSQL_VERSION   | 指定mysql版本。
 MYSQL_HOST_PORT   | 对外映射端口，将映射mysql容器中的`3306`端口。
 MYSQL_ROOT_PASSWORD   | 字符串，将指定mysql的root用户密码。
 MYSQL_USER_NAME   | 字符串，将指定mysql的普通用户名称。
 MYSQL_USER_PASSWORD   | 字符串，将指定mysql的普通用户密码。
 MYSQL_DATA_DIR   | 该值为相对路径，该目录将挂载到mysql容器中的`/var/lib/mysql`目录。
 MYSQL_LOG_DIR   | 该值为相对路径，该目录将挂载到mysql容器中的`/var/log/mysql`目录。
 MYSQL_CONF_FILE   | 该值为相对路径，该文件将映射到mysql容器中的`/etc/mysql/conf.d/mysql.cnf`文件。

REDIS-参数  | 功能
--      | ----------
 REDIS_VERSION   | 指定redis版本。
 REDIS_HOST_PORT   | 对外映射端口，将映射nginx容器中的`6379`端口。
 REDIS_DATA_DIR   | 该值为绝对路径，将挂载到redis容器中的`/data`目录。
 REDIS_PASS_WORD   | 字符串，将指定redis的登录密码。

---

## 1.2 配置 docker-compose.yml
文件: **docker-compose.yml** [配置示例](#yml)

路径: `${YOUR_DOCKER_COMPOSE_YML_PATH}/docker-compose.yml`

说明：您的docker-compose.yml文件所在目录

描述: 运行**deploy-lnmpr-in-docker**的容器服务需要该配置，请根据实际情况进行调整。

---

## 1.3 示例演示

#### 1.3.1 docker-compose <span id="env">.env配置示例</span>

```sh
#.env file

#nginx
WEB_ROOT_DIR=./www
NGINX_VERSION=1.16
NGINX_HTTP_HOST_PORT=80
NGINX_HTTPS_HOST_PORT=443
NGINX_CONF_DIR=./conf/nginx/conf.d
NGINX_CONF_FILE=./conf/nginx/nginx.conf
NGINX_SSL_CERTS_DIR=./conf/nginx/certs
NGINX_LOG_DIR=./log/nginx/

#php
PHP_VERSION=7.2
PHP_PHP_CONF_FILE=./conf/php/php.ini
PHP_FPM_CONF_FILE=./conf/php/php-fpm.d/www.conf
PHP_LOG_DIR=./log/php/
PHP_EXTENSIONS=pdo pdo_mysql mysqli opcache zip

#mysql
MYSQL_VERSION=8.0
MYSQL_HOST_PORT=3306
MYSQL_ROOT_PASSWORD=${YOUR_MYSQL_ROOT_PASSWORD}
MYSQL_USER_NAME=${YOUR_MYSQL_USER_NAME}
MYSQL_USER_PASSWORD=${YOUR_MYSQL_USER_PASSWORD}
MYSQL_DATA_DIR=./data/mysql
MYSQL_LOG_DIR=./log/mysql
MYSQL_CONF_FILE=./conf/mysql/mysql.cnf

#redis
REDIS_VERSION=5.0
REDIS_HOST_PORT=6379
REDIS_DATA_DIR=./data/redis
REDIS_PASS_WORD=${YOUR_REDIS_PASSWORD}

```

#### 1.3.2 docker-compose.yml <span id="yml">配置示例</span>

```sh
#docker-compose.yml file
version: "3.3"

services:
    nginx:
            env_file:
                    - ./.env
            image: nginx:${NGINX_VERSION}-alpine
            container_name: nginx
            volumes:
                    - ${WEB_ROOT_DIR}:/usr/share/nginx/html:rw
                    - ${NGINX_CONF_FILE}:/etc/nginx/nginx.conf:ro
                    - ${NGINX_CONF_DIR}:/etc/nginx/conf.d/:ro
                    - ${NGINX_SSL_CERTS_DIR}:/etc/nginx/certs/:ro
                    - ${NGINX_LOG_DIR}:/var/log/nginx/:rw
            networks:
                    - net-np
            ports:
                    - "${NGINX_HTTP_HOST_PORT}:80"
                    - "${NGINX_HTTPS_HOST_PORT}:443"
            links:
                    - php
            depends_on:
                    - php
                    - gitlab
            restart:
                    always
    php:
            env_file:
                    - ./.env
            build:
                context: ./php-fpm
                args:
                    PHP_VERSION: ${PHP_VERSION}
                    PHP_EXTENSIONS: ${PHP_EXTENSIONS}
            container_name: php
            volumes:
                    - ${WEB_ROOT_DIR}:/usr/share/nginx/html:rw
                    - ${PHP_PHP_CONF_FILE}:/usr/local/etc/php/php.ini:ro
                    - ${PHP_FPM_CONF_FILE}:/usr/local/etc/php-fpm.d/www.conf:rw
                    - ${PHP_LOG_DIR}:/var/log/:rw
            networks:
                    - net-np
                    - net-mp
                    - net-rp
            ports:
                    - "9000"
            links:
                    - mysql
                    - redis
            depends_on:
                    - mysql
            restart:
                    always
    mysql:
            env_file:
                    - ./.env
            image: mysql:${MYSQL_VERSION}
            container_name: mysql
            volumes:
                    - ${MYSQL_CONF_FILE}:/etc/mysql/conf.d/mysql.cnf:ro
                    - ${MYSQL_DATA_DIR}:/var/lib/mysql:rw
                    - ${MYSQL_LOG_DIR}:/var/log/mysql:rw
            networks:
                    - net-mp
            ports:
                    - "${MYSQL_HOST_PORT}:3306"
            environment:
                    - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
                    - MYSQL_USER=${MYSQL_USER_NAME}
                    - MYSQL_PASSWORD=${MYSQL_USER_PASSWORD}
                    - TZ=Asia/Shanghai
            command: ['mysqld', '--character-set-server=utf8mb4']
            restart:
                    always
    redis:
            env_file:
                    - ./.env
            image: redis:${REDIS_VERSION}-alpine
            command: ["redis-server", "--appendonly", "yes", "--requirepass", "${REDIS_PASS_WORD}"]
            container_name: redis
            volumes:
                    - ${REDIS_DATA_DIR}:/data:rw
            networks:
                    - net-rp
            ports:
                    - "${REDIS_HOST_PORT}:6379"
            restart:
                    always
networks:
    net-np:
    net-mp:
    net-rp:


```