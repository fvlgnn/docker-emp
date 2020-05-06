FROM php:7.3-fpm-alpine

ARG XDEBUG
ARG COMPOSER

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
    && docker-php-ext-enable --ini-name 05-opcache.ini opcache

COPY php.ini /usr/local/etc/php/php.ini

RUN apk add --no-cache fcgi

# INSTALL XDEBUG
RUN if [ "$XDEBUG" = "1" ] ; then \
    apk add --no-cache --virtual .build-deps $PHPIZE_DEPS \
    && pecl install xdebug-2.9.5 \
    && docker-php-ext-enable xdebug \
    && apk del -f .build-deps \
	&& echo XDEBUG ENABLED; \
else \
	echo XDEBUG DISABLED; \
fi

# INSTALL COMPOSER
RUN if [ "$COMPOSER" = "1" ] ; then \
    set -xe \
    && apk add --no-cache \
        git \
        curl \
        openssl \
        libressl-dev \
    && docker-php-ext-install \
        phar \
        json \
        iconv \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && chmod +x /usr/local/bin/composer \
    && composer --version \
    # ### example ### install drush
    # && composer global require drush/drush:9.* --prefer-dist \
    # && ln -s /root/.composer/vendor/drush/drush/drush /usr/local/bin/drush \
    # && chmod +x /usr/local/bin/drush \
    && echo COMPOSER INSTALLED; \
else \
	echo COMPOSER SKIPPED; \
fi

# OTHER OPTIONS

# RUN set -xe && echo "pm.status_path = /status" >> /usr/local/etc/php-fpm.d/zz-docker.conf

# RUN mkdir -p var/cache var/logs web/images/products web/images/restaurants web/images/tasks vendor \
#     # Permissions hack because setfacl does not work on Mac and Windows
#     && chown -R www-data web/images \
#     && chmod -R a+w web/images \
#     && chown -R www-data vendor

# ENV APP_ENV dev
# ENV APP_DEBUG 1

WORKDIR /app

CMD ["/bin/bash","-c", "php-fpm"]
