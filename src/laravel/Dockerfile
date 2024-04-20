ARG PHP_VERSION='8.3'
ARG BASE_IMAGE="serversideup/php-dev:311-${PHP_VERSION}-unit-bookworm"

# Learn more about the Server Side Up PHP Docker Images at:
# https://serversideup.net/open-source/docker-php/

FROM ${BASE_IMAGE} AS base

ENV PHP_OPCACHE_ENABLE=1

COPY .bash_aliases /root/.bash_aliases

LABEL maintainer="Qisthi Ramadhani (@ramaID)"

RUN install-php-extensions openswoole

RUN apt update \
    && apt install -y --no-install-recommends git sudo

COPY composer-global-require /usr/bin/composer-global-require

RUN sudo chmod +x /usr/bin/composer-global-require
RUN composer-global-require laravel/installer

COPY --chmod=755 ./entrypoint.d/ /etc/entrypoint.d/
COPY .bash_aliases /root/.bash_aliases
COPY .bashrc /root/.bashrc

RUN composer global require laravel/installer

RUN apt clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*
