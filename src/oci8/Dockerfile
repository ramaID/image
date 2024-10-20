ARG PHP_VERSION='8.2'
ARG BASE_IMAGE="ramageek/image:beta-php${PHP_VERSION}-smart"

FROM ${BASE_IMAGE} AS base

USER root

RUN install-php-extensions openswoole mongodb

# Define Oracle Instant Client version
ARG ORACLE_IC_BASE_URL='https://download.oracle.com/otn_software/linux/instantclient'

# Refer to Oracle Instant Client Basic and SDK version on https://www.oracle.com/database/technologies/instant-client/downloads.html

# For Linux x86-64
RUN if [ "$(uname -m)" = "x86_64" ]; then \
    echo "Downloading Oracle Instant Client for amd64..." && \
    curl -o /tmp/instantclient.zip ${ORACLE_IC_BASE_URL}/1925000/instantclient-basic-linux.x64-19.25.0.0.0dbru.zip && \
    unzip -oq /tmp/instantclient.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient.zip && \
    curl -o /tmp/instantclient-sdk.zip ${ORACLE_IC_BASE_URL}/instantclient-sdk-linuxx64.zip && \
    unzip -oq /tmp/instantclient-sdk.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient-sdk.zip && \
    echo "Moving Instant Client directory..." && \
    mv /usr/lib/oracle/instantclient_19_24 /usr/lib/oracle/instantclient_19_23; \
fi

# For Linux ARM (aarch64)
RUN if [ "$(uname -m)" = "aarch64" ]; then \
    echo "Downloading Oracle Instant Client for arm64..." && \
    curl -o /tmp/instantclient.zip ${ORACLE_IC_BASE_URL}/1924000/instantclient-basic-linux.arm64-19.24.0.0.0dbru.zip && \
    unzip -oq /tmp/instantclient.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient.zip && \
    curl -o /tmp/instantclient-sdk.zip ${ORACLE_IC_BASE_URL}/1923000/instantclient-sdk-linux-arm64.zip && \
    unzip -oq /tmp/instantclient-sdk.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient-sdk.zip; \
fi

# For Linux x86
RUN if [ "$(uname -m)" = "i686" ]; then \
    echo "Downloading Oracle Instant Client for x86..." && \
    curl -o /tmp/instantclient.zip ${ORACLE_IC_BASE_URL}/1924000/instantclient-basic-linux-19.24.0.0.0dbru.zip && \
    unzip -oq /tmp/instantclient.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient.zip && \
    curl -o /tmp/instantclient-sdk.zip ${ORACLE_IC_BASE_URL}/instantclient-sdk-linux.zip && \
    unzip -oq /tmp/instantclient-sdk.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient-sdk.zip; \
fi

# For z/Linux (64-bit)
RUN if [ "$(uname -m)" = "s390x" ]; then \
    echo "Downloading Oracle Instant Client for z/Linux (64-bit)..." && \
    curl -o /tmp/instantclient.zip ${ORACLE_IC_BASE_URL}/1924000/instantclient-basic-linux.zseries64-19.24.0.0.0dbru.zip && \
    unzip -oq /tmp/instantclient.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient.zip && \
    curl -o /tmp/instantclient-sdk.zip ${ORACLE_IC_BASE_URL}/instantclient-sdk-linuxz64.zip && \
    unzip -oq /tmp/instantclient-sdk.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient-sdk.zip; \
fi

RUN echo /usr/lib/oracle/instantclient > /etc/ld.so.conf.d/oracle-instantclient.conf && \
    ldconfig && \
    apt-get update && apt-get install -y \
        libaio1 \
        zlib1g-dev \
        libpng-dev \
        libjpeg-dev \
        libfreetype6-dev && \
    rm -rf /var/lib/apt/lists/*

ENV LD_LIBRARY_PATH /usr/lib/oracle/instantclient_19_23

RUN docker-php-ext-configure oci8 --with-oci8=instantclient,/usr/lib/oracle/instantclient_19_23 && \
    docker-php-ext-install -j$(nproc) oci8 && \
    # Install the PHP gd library
    docker-php-ext-configure gd \
        --with-jpeg=/usr/include/ \
        --with-freetype=/usr/include/ && \
    docker-php-ext-install gd

FROM base AS deploy
COPY --chown=www-data:www-data . /var/www/html
USER www-data
