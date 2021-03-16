FROM composer:latest as builder

ADD www/ /app/
RUN composer install

FROM php:7.4.0-apache

RUN apt-get update
RUN apt-get dist-upgrade -y
RUN apt-get install -y \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev
RUN apt-get clean

# Enable Apache modules
RUN a2enmod \
    deflate \
    expires \
    headers \
    remoteip \
    rewrite

# PHP Extensions
RUN docker-php-ext-install -j$(nproc) \
    opcache \
    pdo_mysql

# Apache configuration
#ADD conf/vhost.conf /etc/apache2/sites-available/000-default.conf
#ADD conf/apache.conf /etc/apache2/conf-available/z-app.conf
#RUN a2enconf z-app

# php-ext
RUN docker-php-ext-install mysqli
RUN docker-php-ext-configure gd --with-freetype --with-jpeg
RUN docker-php-ext-install -j$(nproc) gd

# Redis
RUN pecl install redis
RUN docker-php-ext-enable redis

COPY --from=builder /app/ /var/www/html/