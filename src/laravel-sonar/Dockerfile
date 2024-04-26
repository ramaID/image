ARG PHP_VERSION='8.3'
ARG BASE_IMAGE="serversideup/php:beta-${PHP_VERSION}-unit-bookworm"

FROM ${BASE_IMAGE} AS base

LABEL maintainer="Qisthi Ramadhani (@ramaID)"

ENV PHP_OPCACHE_ENABLE=1

USER root

COPY composer-global-require /usr/bin/composer-global-require

RUN install-php-extensions xdebug openswoole mongodb gd exif bcmath \
    && apt update \
    && apt install -y --no-install-recommends git \
    && chmod +x /usr/bin/composer-global-require \
    && curl https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip --output /tmp/sonar-scanner-cli-5.0.1.3006-linux.zip \
    && unzip /tmp/sonar-scanner-cli-5.0.1.3006-linux.zip -d /opt \
    && ln -s /opt/sonar-scanner-5.0.1.3006-linux/bin/sonar-scanner /usr/bin/sonar-scanner

RUN apt clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

# Drop back to our unprivileged user
USER www-data

COPY .bash_aliases /var/www/.bash_aliases
COPY .bashrc /var/www/.bashrc

RUN composer global config allow-plugins.isaac/composer-velocita true \
    && composer-global-require laravel/installer isaac/composer-velocita
