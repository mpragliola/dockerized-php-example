# ---------------------------------------------------------------------------
FROM php:7.2-fpm-alpine AS builder

# Install generally needed PHP extensions here, with dependenceis first
RUN apk add --no-cache \
    libxml2-dev \
    icu-libs icu-dev \
    libzip-dev

RUN docker-php-ext-install intl bcmath mbstring opcache pdo pdo_mysql zip \
    && docker-php-ext-configure opcache --enable-opcache

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && php -r "unlink('composer-setup.php');"

# ---------------------------------------------------------------------------
FROM php:7.2-fpm-alpine AS base

COPY --from=builder /usr/lib/ /usr/lib/
COPY --from=builder /usr/bin/ /usr/bin/
COPY --from=builder /usr/local/bin/ /usr/local/bin/
COPY --from=builder /usr/local/etc/ /usr/local/etc/
COPY --from=builder /usr/local/lib/ /usr/local/lib/
COPY --from=builder /usr/local/sbin/ /usr/local/sbin/

# ---------------------------------------------------------------------------
FROM base AS dev

ENV APP_ENV="dev"

# Install XDebug
RUN apk add --no-cache --virtual .pecl-build-deps autoconf g++ make \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
    && apk del --no-cache --purge .pecl-build-deps
# ---------------------------------------------------------------------------
FROM base AS test

ENV APP_ENV="test"

# ---------------------------------------------------------------------------
FROM base AS prod

ENV APP_ENV="prod"

RUN echo ${DEST_SRC}
COPY --from=composer /usr/bin/composer /usr/bin/composer
COPY --chown=www-data:www-data src /var/www/src/
WORKDIR /var/www/src/
USER www-data

RUN composer install \
    --prefer-dist \
    --no-dev \
    --optimize-autoloader \
    --no-interaction \
    --no-progress \
    --no-scripts \
    --no-ansi
