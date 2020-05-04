FROM php:7.3-fpm-alpine

RUN apk update

RUN apk add --no-cache \
        bash \
        zlib

RUN set -xe \
    && apk add --no-cache \
        icu-dev \
        zlib-dev \
    && apk add --no-cache \
        libpng \
        libjpeg-turbo \
        freetype \
        libpng-dev \
        libjpeg-turbo-dev \
        freetype-dev \
    && apk add --no-cache \
        unzip \
        libzip-dev \
    && apk add --no-cache \
        jpegoptim \
        optipng \
        pngquant \
        openssh-server \
        openssh-client \
    && docker-php-ext-install \
        pdo_mysql \
        mysqli \
    && docker-php-ext-configure gd \
        --with-gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install \
        intl \
        zip \
        gd \
        opcache \
    && docker-php-ext-enable --ini-name 05-opcache.ini opcache

COPY php.ini /usr/local/etc/php/php.ini

RUN apk add --no-cache fcgi

RUN if [ "$XDEBUG" = "1" ] ; then pecl install xdebug-2.8.1 && docker-php-ext-enable xdebug; else echo XDEBUG DISABLED; fi

# RUN set -xe && echo "pm.status_path = /status" >> /usr/local/etc/php-fpm.d/zz-docker.conf

# RUN mkdir -p var/cache var/logs web/images/products web/images/restaurants web/images/tasks vendor \
#     # Permissions hack because setfacl does not work on Mac and Windows
#     && chown -R www-data web/images \
#     && chmod -R a+w web/images \
#     && chown -R www-data vendor

# ENV APP_ENV dev
# ENV APP_DEBUG 1

WORKDIR /srv/app

# CMD ["/bin/bash","-c", "php-fpm"]
CMD ["/bin/bash","-c","exec php-fpm","-F"]
