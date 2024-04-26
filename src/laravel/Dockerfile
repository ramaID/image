ARG PHP_VERSION='8.3'
ARG BASE_IMAGE="serversideup/php:beta-${PHP_VERSION}-unit-bookworm"

FROM ${BASE_IMAGE} AS base

ENV PHP_OPCACHE_ENABLE=1

USER root

COPY .bash_aliases /root/.bash_aliases

LABEL maintainer="Qisthi Ramadhani (@ramaID)"

COPY composer-global-require /usr/bin/composer-global-require

RUN install-php-extensions openswoole mongodb gd exif bcmath \
    && apt update \
    && apt install -y --no-install-recommends git \
    && chmod +x /usr/bin/composer-global-require \
    && apt clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

# Drop back to our unprivileged user
USER www-data

RUN composer-global-require laravel/installer

COPY .bash_aliases /var/www/.bash_aliases
COPY .bashrc /var/www/.bashrc

RUN composer global config allow-plugins.isaac/composer-velocita true \
    && composer global require laravel/installer isaac/composer-velocita

# RUN composer velocita:enable http://192.168.88.210:8999/
