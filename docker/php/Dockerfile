ARG NODE_VERSION=20.18.0

FROM node:${NODE_VERSION}-alpine AS node
FROM composer:2 as composer
FROM php:8.3-fpm-alpine as base

# Necessary tools
RUN apk add --update --no-cache ${PHPIZE_DEPS} git curl

# ZIP module
RUN apk add --no-cache libzip-dev && docker-php-ext-configure zip && docker-php-ext-install zip

# Imagick module
RUN apk add --no-cache libgomp imagemagick imagemagick-dev && \
    pecl install -o -f imagick && \
    docker-php-ext-enable imagick

# Intl module
RUN set -xe \
    && apk add --update \
    icu \
    && apk add --no-cache --virtual .php-deps \
    make \
    && apk add --no-cache --virtual .build-deps \
    $PHPIZE_DEPS \
    zlib-dev \
    icu-dev \
    g++ \
    && docker-php-ext-configure intl \
    && docker-php-ext-install \
    intl \
    && docker-php-ext-enable intl \
    && { find /usr/local/lib -type f -print0 | xargs -0r strip --strip-all -p 2>/dev/null || true; } \
    && apk del .build-deps \
    && rm -rf /tmp/* /usr/local/lib/php/doc/* /var/cache/apk/*

# Opcache
RUN apk add --no-cache $PHPIZE_DEPS \
    && docker-php-ext-install opcache

# Mysql
RUN docker-php-ext-install mysqli pdo pdo_mysql && docker-php-ext-enable pdo_mysql

# Xdebug
RUN apk add --no-cache $PHPIZE_DEPS linux-headers \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug

# Symfony CLI tool
RUN apk add --no-cache bash && \
    curl -1sLf 'https://dl.cloudsmith.io/public/symfony/stable/setup.alpine.sh' | bash && \
    apk add symfony-cli

# Necessary build deps not longer needed
RUN apk del --no-cache ${PHPIZE_DEPS}

# Composer
COPY --from=composer /usr/bin/composer /usr/local/bin/composer

# Node and npm
COPY --from=node /usr/lib /usr/lib
COPY --from=node /usr/local/lib /usr/local/lib
COPY --from=node /usr/local/include /usr/local/include
COPY --from=node /usr/local/bin /usr/local/bin

# Clean up image
RUN rm -rf /tmp/* /var/cache

#RUN chown -R www-data:www-data /var/www

ARG USER_UID=82
ARG USER_GID=82

# Recreate www-data user with user id matching the host
RUN deluser --remove-home www-data && \
    addgroup -S -g ${USER_GID} www-data && \
    adduser -u ${USER_UID} -D -S -G www-data www-data

USER www-data
RUN cd /var/www/html && rm -rf public
