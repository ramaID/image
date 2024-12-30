ARG NODE_VERSION='18.20.5'
ARG PHP_VERSION='8.3'
ARG BASE_IMAGE="serversideup/php:${PHP_VERSION}-cli-alpine"

FROM node:${NODE_VERSION}-alpine AS node

FROM ${BASE_IMAGE}

LABEL maintainer="Qisthi Ramadhani (@ramaID)"

USER root

# Install node and npm
COPY --from=node /usr/lib /usr/lib
COPY --from=node /usr/local/lib /usr/local/lib
COPY --from=node /usr/local/include /usr/local/include
COPY --from=node /usr/local/bin /usr/local/bin

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
RUN npm install -g bun@latest
RUN if [[ ! $(uname -m) == "aarch64" ]] ; \
    then \
    rm /usr/local/bin/bun ; \
    ln -s /usr/local/lib/node_modules/bun/node_modules/@oven/bun-linux-x64-baseline/bin/bun /usr/local/bin/bun ; \
    fi

RUN install-php-extensions openswoole mongodb gd exif bcmath
RUN apk update && apk add --no-cache git unzip bash
RUN curl https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-6.2.1.4610-linux-x64.zip --output /tmp/sonar-scanner.zip \
    && unzip /tmp/sonar-scanner.zip -d /opt \
    && ln -s /opt/sonar-scanner-6.2.1.4610-linux-x64/bin/sonar-scanner /usr/bin/sonar-scanner

# Clean up
RUN rm -rf /var/cache/apk/* && \
    rm -rf /tmp/* /var/tmp/*

# Drop back to our unprivileged user
USER www-data

RUN composer global config allow-plugins.gmta/composer-velocita true \
    && composer global require gmta/composer-velocita
