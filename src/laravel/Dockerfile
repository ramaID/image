# Learn more about the Server Side Up PHP Docker Images at:
# https://serversideup.net/open-source/docker-php/

FROM serversideup/php:beta-8.3-unit AS base

COPY .bash_aliases /root/.bash_aliases

LABEL maintainer="Qisthi Ramadhani (@ramaID)"

FROM base AS development

# Fix permission issues in development by setting the "www-data"
# user to the same user and group that is running docker.
ARG USER_ID
ARG GROUP_ID
RUN docker-php-serversideup-set-id www-data ${USER_ID} ${GROUP_ID}

RUN install-php-extensions opcache imagick mongodb openswoole

FROM base AS deploy
COPY --chown=www-data:www-data . /var/www/html
