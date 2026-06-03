# Release tag: php-8.4-openswoole-<x.y.z>
# Output image: ghcr.io/airygen/php:8.4-openswoole-<x.y.z>
# Versioning rules:
#   MAJOR  PHP/Alpine major version change, extension removal, breaking ini change
#   MINOR  New extension, Alpine minor bump, new optional settings
#   PATCH  Extension patch bump, bug fix, default ini tweak
# See README.md in the project root for details

FROM php:8.4-cli-alpine3.23
LABEL org.opencontainers.image.source=https://github.com/airygen/docker-images

# See https://pkgs.alpinelinux.org/packages
ENV BUILD_DEPS \
  libpng-dev \
  libjpeg-turbo-dev \
  libwebp-dev \
  freetype-dev \
  libzip-dev \
  libxml2-dev \
  openssl-dev \
  linux-headers

# Install dependencies and PHP extensions
RUN set -xe && \
  apk add --no-cache --virtual .phpize-deps ${PHPIZE_DEPS} \
  && \
  apk add --no-cache --virtual .build-deps ${BUILD_DEPS} \
  && \
  apk add --no-cache \
  libstdc++ \
  libpng \
  libjpeg-turbo \
  libwebp \
  freetype \
  libzip \
  libxml2 \
  tzdata \
  rsyslog \
  && \
  # See https://github.com/docker-library/php/issues/912#issuecomment-559918036
  docker-php-ext-configure gd --with-jpeg --with-freetype --with-webp \
  && \
  MAKEFLAGS="-j$(getconf _NPROCESSORS_ONLN)" pecl install \
  openswoole-25.2.0 \
  redis-6.2.0 \
  && \
  docker-php-ext-enable \
  openswoole \
  redis \
  opcache \
  && \
  docker-php-ext-install -j $(getconf _NPROCESSORS_ONLN) \
  gd \
  zip \
  bcmath \
  exif \
  sockets \
  pcntl \
  pdo_mysql \
  && \
  apk del --no-cache .phpize-deps .build-deps \
  && \
  php -m

RUN echo "opcache.enable_cli=1" >> "$PHP_INI_DIR/conf.d/docker-php-ext-opcache.ini"

ENV TZ=Asia/Taipei

WORKDIR /var/www/html
