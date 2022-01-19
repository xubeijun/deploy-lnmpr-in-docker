######
# See: https://hub.docker.com/_/php/
######

ARG PHP_VERSION
FROM php:${PHP_VERSION}-fpm
MAINTAINER xubeijun

######
# You can install php extensions using docker-php-ext-install
######

ARG PHP_EXTENSIONS

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
    && apt-get install -y zlib1g-dev \
    && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-install -j$(nproc) iconv \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install -j$(nproc) ${PHP_EXTENSIONS}

RUN pecl install redis-4.3.0 \
    && docker-php-ext-enable redis