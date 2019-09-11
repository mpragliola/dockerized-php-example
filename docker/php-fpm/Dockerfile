# ---------------------------------------------------------------------------
FROM php:7.2-fpm-alpine AS builder

# Install generally needed PHP extensions here, with dependenceis first
RUN apk add --no-cache libxml2-dev icu-dev
RUN docker-php-ext-install bcmath mbstring intl opcache pdo pdo_mysql zip

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

COPY --chown=www-data:www-data src /var/www/src

ENV APP_ENV="prod"
