# Learn more about the Server Side Up PHP Docker Images at:
# https://serversideup.net/open-source/docker-php/

FROM serversideup/php:beta-8.3-unit AS base

COPY .bash_aliases /root/.bash_aliases

LABEL maintainer="Qisthi Ramadhani (@ramaID)"

COPY composer-global-require /usr/bin/composer-global-require

RUN chmod +x /usr/bin/composer-global-require
RUN composer-global-require laravel/installer

COPY --chmod=755 ./entrypoint.d/ /etc/entrypoint.d/
COPY .bash_aliases /root/.bash_aliases
COPY .bashrc /root/.bashrc

RUN composer global require laravel/installer

RUN install-php-extensions opcache
RUN install-php-extensions openswoole
