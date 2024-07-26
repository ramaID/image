ARG PHP_VERSION='8.3'
ARG BASE_IMAGE="serversideup/php:${PHP_VERSION}-fpm-nginx"

############################################
# Base Image
############################################

# Learn more about the Server Side Up PHP Docker Images at:
# https://serversideup.net/open-source/docker-php/
FROM ${BASE_IMAGE} AS base

# Switch to root before installing our PHP extensions
USER root
RUN install-php-extensions openswoole mongodb gd exif bcmath imagick intl

RUN apt update \
    && apt install -y --no-install-recommends git \
    && apt clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

COPY composer-global-require /usr/bin/composer-global-require
RUN chmod +x /usr/bin/composer-global-require

# For linux/amd64
RUN if [ "$(uname -m)" = "x86_64" ]; then \
    curl -o /tmp/instantclient.zip https://download.oracle.com/otn_software/linux/instantclient/1924000/instantclient-basic-linux.x64-19.23.0.0.0dbru.zip && \
    unzip -oq /tmp/instantclient.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient.zip && \
    curl -o /tmp/instantclient-sdk.zip https://download.oracle.com/otn_software/linux/instantclient/1924000/instantclient-sdk-linux.x64-19.23.0.0.0dbru.zip && \
    unzip -oq /tmp/instantclient-sdk.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient-sdk.zip && \
    mv /usr/lib/oracle/instantclient_19_24 /usr/lib/oracle/instantclient_19_23; \
fi

# For linux/arm64/v8
RUN if [ "$(uname -m)" = "aarch64" ]; then \
    curl -o /tmp/instantclient.zip https://download.oracle.com/otn_software/linux/instantclient/1923000/instantclient-basic-linux.arm64-19.23.0.0.0dbru.zip && \
    unzip -oq /tmp/instantclient.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient.zip && \
    curl -o /tmp/instantclient-sdk.zip https://download.oracle.com/otn_software/linux/instantclient/1923000/instantclient-sdk-linux.arm64-19.23.0.0.0dbru.zip && \
    unzip -oq /tmp/instantclient-sdk.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient-sdk.zip; \
fi

# For linux/arm/v7
RUN if [ "$(uname -m)" = "armv7l" ]; then \
    curl -o /tmp/instantclient.zip https://download.oracle.com/otn_software/linux/instantclient/1923000/instantclient-basic-linux-19.23.0.0.0dbru.zip && \
    unzip -oq /tmp/instantclient.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient.zip && \
    curl -o /tmp/instantclient-sdk.zip https://download.oracle.com/otn_software/linux/instantclient/1923000/instantclient-sdk-linux-19.23.0.0.0dbru.zip && \
    unzip -oq /tmp/instantclient-sdk.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient-sdk.zip; \
fi

RUN echo /usr/lib/oracle/instantclient_19_23 > /etc/ld.so.conf.d/oracle-instantclient.conf && \
    ldconfig

ENV LD_LIBRARY_PATH /usr/lib/oracle/instantclient_19_23

RUN docker-php-ext-configure oci8 --with-oci8=instantclient,/usr/lib/oracle/instantclient_19_23 \
    && docker-php-ext-install -j$(nproc) oci8

############################################
# Development Image
############################################
FROM base AS development

RUN install-php-extensions xdebug

# We can pass USER_ID and GROUP_ID as build arguments
# to ensure the www-data user has the same UID and GID
# as the user running Docker.
ARG USER_ID
ARG GROUP_ID

# Switch to root so we can set the user ID and group ID
USER root
RUN docker-php-serversideup-set-id www-data $USER_ID:$GROUP_ID  && \
    docker-php-serversideup-set-file-permissions --owner $USER_ID:$GROUP_ID --service nginx

# Switch back to the unprivileged www-data user
USER www-data

############################################
# CI image
############################################
FROM base AS ci

# Sometimes CI images need to run as root
# so we set the ROOT user and configure
# the PHP-FPM pool to run as www-data
USER root
RUN echo "user = www-data" >> /usr/local/etc/php-fpm.d/docker-php-serversideup-pool.conf && \
    echo "group = www-data" >> /usr/local/etc/php-fpm.d/docker-php-serversideup-pool.conf

############################################
# Production Image
############################################
FROM base AS deploy
COPY --chown=www-data:www-data . /var/www/html
USER www-data

COPY .bash_aliases /var/www/.bash_aliases
COPY .bashrc /var/www/.bashrc

RUN composer global config allow-plugins.gmta/composer-velocita true \
    && composer global require gmta/composer-velocita
