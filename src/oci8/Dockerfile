ARG PHP_VERSION='8.2'
ARG BASE_IMAGE="ramageek/image:php${PHP_VERSION}-multi-stage"

FROM ${BASE_IMAGE} AS base

USER root

# Define Oracle Instant Client version
ARG ORACLE_IC_VERSION='19.23.0.0.0dbru'
ARG ORACLE_IC_BASE_URL='https://download.oracle.com/otn_software/linux/instantclient'

# For linux/amd64
RUN if [ "$(uname -m)" = "x86_64" ]; then \
    echo "Downloading Oracle Instant Client for amd64..." && \
    curl -o /tmp/instantclient.zip ${ORACLE_IC_BASE_URL}/1924000/instantclient-basic-linux.x64-${ORACLE_IC_VERSION}.zip && \
    unzip -oq /tmp/instantclient.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient.zip && \
    curl -o /tmp/instantclient-sdk.zip ${ORACLE_IC_BASE_URL}/1924000/instantclient-sdk-linux.x64-${ORACLE_IC_VERSION}.zip && \
    unzip -oq /tmp/instantclient-sdk.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient-sdk.zip && \
    echo "Moving Instant Client directory..." && \
    mv /usr/lib/oracle/instantclient_19_24 /usr/lib/oracle/instantclient_19_23; \
fi

# For linux/arm64/v8
RUN if [ "$(uname -m)" = "aarch64" ]; then \
    echo "Downloading Oracle Instant Client for arm64..." && \
    curl -o /tmp/instantclient.zip ${ORACLE_IC_BASE_URL}/1923000/instantclient-basic-linux.arm64-${ORACLE_IC_VERSION}.zip && \
    unzip -oq /tmp/instantclient.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient.zip && \
    curl -o /tmp/instantclient-sdk.zip ${ORACLE_IC_BASE_URL}/1923000/instantclient-sdk-linux.arm64-${ORACLE_IC_VERSION}.zip && \
    unzip -oq /tmp/instantclient-sdk.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient-sdk.zip; \
fi

# For linux/arm/v7
RUN if [ "$(uname -m)" = "armv7l" ]; then \
    echo "Downloading Oracle Instant Client for arm/v7..." && \
    curl -o /tmp/instantclient.zip ${ORACLE_IC_BASE_URL}/1923000/instantclient-basic-linux-${ORACLE_IC_VERSION}.zip && \
    unzip -oq /tmp/instantclient.zip -d /usr/lib/oracle && \
    rm /tmp/instantclient.zip && \
    curl -o /tmp/instantclient-sdk.zip ${ORACLE_IC_BASE_URL}/1923000/instantclient-sdk-linux-${ORACLE_IC_VERSION}.zip && \
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
