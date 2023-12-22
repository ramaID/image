ARG PHP_VERSION='8.3'

FROM shinsenter/laravel:php${PHP_VERSION}

LABEL maintainer="Qisthi Ramadhani (@ramaID)"

# ==========================================================

# you may want to install some PHP modules

# e.g: the following line will install imagick, pgsql, solr modules

RUN phpaddmod pgsql mongodb

# ==========================================================

# Control your timezone

ENV TZ="UTC"

# sets GID and UID

ENV PUID=9999
ENV PGID=9999

# sets web server root path

ENV WEBHOME="/var/www/html"

# ==========================================================

# The project name to be pulled by "composer create-project"

# Default: ENV LARAVEL_PROJECT="laravel/laravel"

# Example: ENV LARAVEL_PROJECT="phanan/koel"

ENV LARAVEL_PROJECT="laravel/laravel"

# Optimize and cache all config, views, routes

ENV LARAVEL_AUTO_OPTIMIZE=false

# Create symlinks to the storage folder

ENV LARAVEL_LINK_STORAGE=true

# Run Laravel migrations (for development purposese)

ENV LARAVEL_AUTO_MIGRATION=false

# Auto start artisan queue:work when container is up

ENV LARAVEL_QUEUE_ENABLED=false

# Extra arguments for artisan queue:work

# Example: ENV LARAVEL_QUEUE_OPTIONS="--timeout=10 --tries=3 redis"

ENV LARAVEL_QUEUE_OPTIONS=

# Auto start artisan schedule:work when container is up

ENV LARAVEL_SCHEDULE_ENABLED=false

# Extra arguments for artisan schedule:work

ENV LARAVEL_SCHEDULE_OPTIONS=

# ==========================================================

# You can easily change PHP-FPM configurations

# by using pre-defined Docker's environment variables.

# Learn more: https://code.shin.company/php#customize-docker-image

COPY .bash_aliases /root/.bash_aliases
