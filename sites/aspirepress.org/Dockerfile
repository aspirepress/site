FROM dunglas/frankenphp:1.4.1-php8.4.3-bookworm AS base

COPY --from=composer:2.8.5 /usr/bin/composer /usr/bin/composer

RUN install-php-extensions bcmath exif gd intl mysqli zip imagick opcache

RUN useradd --create-home --shell /bin/bash app \
    && chown -R app:app /config /data \
    && apt update \
    && apt install -y default-mysql-client

COPY docker/ap-site-wordpress/Caddyfile /etc/caddy/Caddyfile
COPY docker/ap-site-wordpress/php.local.ini /usr/local/etc/php/conf.d/php.local.ini

WORKDIR /app
CMD ["frankenphp", "run", "--config", "/etc/caddy/Caddyfile"]

################
FROM base AS dev
USER app

################
FROM base AS prod

RUN rm -rf /app
COPY . /app
RUN chown -R app:app /app

USER app
RUN composer install --no-dev --no-interaction --no-progress --optimize-autoloader --working-dir=/app

