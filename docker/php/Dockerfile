FROM php:7.3-fpm-alpine
LABEL maintainer "tkumai"

ARG UID=911
ARG UNAME=www
ARG GID=911
ARG GNAME=www

ENV WORKDIR=/var/www/html
WORKDIR $WORKDIR

COPY ./docker/php/php.ini /usr/local/etc/php/php.ini
COPY ./src/composer.json ./src/composer.lock ${WORKDIR}/

RUN set -x \
  && apk add --no-cache git php7-zlib zlib-dev libzip-dev \
  && docker-php-ext-install pdo_mysql zip \
  && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
  && composer install --no-autoloader --no-progress --no-dev

COPY ./src .
COPY ./src/.env.example .env

RUN set -x \
  && composer install --no-progress --no-dev \
  && php artisan config:clear \
  && php artisan key:generate \
  && addgroup ${GNAME} -g ${GID} \
  && adduser -D -G ${GNAME} -u ${UID} ${UNAME} \
  && chown -R ${UNAME}:${GNAME} ${WORKDIR} \
  && mv /root/.composer /home/${UNAME}/ \
  && chown -R ${UNAME}:${GNAME} /home/${UNAME}

USER ${UNAME}