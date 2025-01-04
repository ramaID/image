ARG NODE_VERSION='18.20.5'
# only support PHP > 8.2
ARG PHP_VERSION='8.3'
ARG BASE_IMAGE="dunglas/frankenphp:1.3.6-php${PHP_VERSION}-alpine"

FROM node:${NODE_VERSION}-alpine AS node

FROM oven/bun:alpine AS bun

FROM ${BASE_IMAGE}

LABEL maintainer="Qisthi Ramadhani (https://qisthi.dev)"

ENV SERVER_NAME="http://"

RUN apk update && \
    apk add --no-cache git unzip bash && \
    install-php-extensions gd pcntl opcache pdo pdo_mysql pdo_pgsql mongodb redis zip

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Install node and npm
COPY --from=node /usr/lib /usr/lib
COPY --from=node /usr/local/lib /usr/local/lib
COPY --from=node /usr/local/include /usr/local/include
COPY --from=node /usr/local/bin /usr/local/bin
COPY --from=node /opt/yarn-v1.22.22 /opt/yarn-v1.22.22

# Install glibc to run bun
RUN if [[ $(uname -m) == "aarch64" ]] ; \
    then \
        # aarch64
        wget https://raw.githubusercontent.com/squishyu/alpine-pkg-glibc-aarch64-bin/master/glibc-2.26-r1.apk ; \
        apk add --no-cache --allow-untrusted --force-overwrite glibc-2.26-r1.apk ; \
        rm glibc-2.26-r1.apk ; \
    else \
        # x86_64
        wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.28-r0/glibc-2.28-r0.apk ; \
        apk add --no-cache --allow-untrusted --force-overwrite glibc-2.28-r0.apk ; \
        rm glibc-2.28-r0.apk ; \
    fi

# Install bun
COPY --from=bun /usr/local/bin /usr/local/bin

# Clean up
RUN rm -rf /var/cache/apk/* && \
    rm -rf /tmp/* /var/tmp/*

WORKDIR /var/www/html

RUN composer global config allow-plugins.gmta/composer-velocita true && \
    composer global require gmta/composer-velocita

COPY shell/aliases.sh /var/www/.aliases.sh
COPY shell/bashrc /var/www/.bashrc

# Start with base PHP config, then add extensions.
COPY php.ini /usr/local/etc/php/
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

EXPOSE 80
