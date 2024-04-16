# Learn more about the Server Side Up PHP Docker Images at:
# https://serversideup.net/open-source/docker-php/

FROM serversideup/php:beta-8.3-unit AS base

ENV PHP_OPCACHE_ENABLE=1

COPY .bash_aliases /root/.bash_aliases

LABEL maintainer="Qisthi Ramadhani (@ramaID)"

COPY composer-global-require /usr/bin/composer-global-require

RUN chmod +x /usr/bin/composer-global-require
RUN composer-global-require laravel/installer

COPY --chmod=755 ./entrypoint.d/ /etc/entrypoint.d/
COPY .bash_aliases /root/.bash_aliases
COPY .bashrc /root/.bashrc

RUN composer global require laravel/installer

RUN install-php-extensions openswoole

# Install PHP Imagemagick using regular Ubuntu commands
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && curl https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip --output /tmp/sonar-scanner-cli-5.0.1.3006-linux.zip \
    && unzip /tmp/sonar-scanner-cli-5.0.1.3006-linux.zip -d /opt \
    && ln -s /opt/sonar-scanner-5.0.1.3006-linux/bin/sonar-scanner /usr/bin/sonar-scanner \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*
