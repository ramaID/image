ARG PHP_VERSION='8.3'
ARG BASE_IMAGE="serversideup/php:${PHP_VERSION}-fpm-nginx"

FROM ${BASE_IMAGE} AS base

LABEL maintainer="Qisthi Ramadhani (@ramaID)"

ENV PHP_OPCACHE_ENABLE=1

USER root

COPY composer-global-require /usr/bin/composer-global-require

RUN install-php-extensions openswoole mongodb gd exif bcmath \
    && apt update \
    && apt install -y --no-install-recommends git \
    && chmod +x /usr/bin/composer-global-require \
    && apt clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
    && chown -R www-data:www-data /var/www

# Drop back to our unprivileged user
USER www-data

COPY .bash_aliases /var/www/.bash_aliases
COPY .bashrc /var/www/.bashrc

RUN composer global config allow-plugins.gmta/composer-velocita true \
    && composer global require gmta/composer-velocita
