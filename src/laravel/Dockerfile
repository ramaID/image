ARG PHP_VERSION='8.3'
ARG BASE_IMAGE="shinsenter/php:${PHP_VERSION}-cli"

FROM ${BASE_IMAGE}

LABEL maintainer="Qisthi Ramadhani (@ramaID)"

RUN apt update \
    && apt install -y --no-install-recommends git php${PHP_VERSION}-pgsql php${PHP_VERSION}-mongodb \
    && apt clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

RUN composer global require "laravel/installer"

ENV PATH="${PATH}:/composer/vendor/bin"

COPY .bash_aliases /root/.bash_aliases
