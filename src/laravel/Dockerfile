ARG PHP_VERSION='8.3'
ARG BASE_IMAGE="serversideup/php:beta-${PHP_VERSION}-unit"

# Learn more about the Server Side Up PHP Docker Images at:
# https://serversideup.net/open-source/docker-php/

# FROM serversideup/php:beta-${}-unit AS base
FROM ${BASE_IMAGE} AS base

ENV PHP_OPCACHE_ENABLE=1

COPY .bash_aliases /root/.bash_aliases

LABEL maintainer="Qisthi Ramadhani (@ramaID)"

COPY composer-global-require /usr/bin/composer-global-require

RUN chmod +x /usr/bin/composer-global-require
RUN composer-global-require laravel/installer

COPY --chmod=755 ./entrypoint.d/ /etc/entrypoint.d/
COPY .bash_aliases /root/.bash_aliases
COPY .bashrc /root/.bashrc

RUN composer global require laravel/installer

RUN install-php-extensions openswoole

# Install PHP Imagemagick using regular Ubuntu commands
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*
