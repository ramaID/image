ARG NODE_VERSION='18.20.5'
ARG PHP_VERSION='8.3'
ARG BASE_IMAGE="serversideup/php:${PHP_VERSION}-cli-alpine"

FROM node:${NODE_VERSION}-alpine AS node

FROM oven/bun:alpine AS bun

FROM ${BASE_IMAGE}

LABEL maintainer="Qisthi Ramadhani (https://qisthi.dev)"

USER root

RUN install-php-extensions openswoole mongodb gd exif bcmath \
    && apk update && apk add --no-cache git unzip bash

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

# Install sonar-scanner
RUN if [[ $(uname -m) == "aarch64" ]] ; \
    then \
        # aarch64
        curl https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-6.2.1.4610-linux-aarch64.zip --output /tmp/sonar-scanner.zip && \
        export SONAR_DIR="sonar-scanner-6.2.1.4610-linux-aarch64" ; \
    else \
        # x86_64
        curl https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-6.2.1.4610-linux-x64.zip --output /tmp/sonar-scanner.zip && \
        export SONAR_DIR="sonar-scanner-6.2.1.4610-linux-x64" ; \
    fi && \
    unzip /tmp/sonar-scanner.zip -d /opt && \
    ln -s /opt/${SONAR_DIR}/bin/sonar-scanner /usr/local/bin/sonar-scanner && \
    rm /tmp/sonar-scanner.zip

# Clean up
RUN rm -rf /var/cache/apk/* && \
    rm -rf /tmp/* /var/tmp/*

# Drop back to our unprivileged user
USER www-data

RUN composer global config allow-plugins.gmta/composer-velocita true \
    && composer global require gmta/composer-velocita

# Copy shell aliases to both root and www-data user homes
COPY shell/aliases.sh /root/.aliases.sh
COPY shell/aliases.sh /var/www/.aliases.sh

# Copy shell configuration
COPY shell/bashrc /root/.sh
COPY shell/bashrc /var/www/.bashrc
