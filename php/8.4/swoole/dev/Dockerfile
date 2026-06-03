# Release tag: php-8.4-swoole-dev-<x.y.z>
# Output image: ghcr.io/airygen/php:8.4-swoole-dev-<x.y.z>
# This image adds development tools (composer, xdebug, vim, node) on top of the production image.
#
# Use cases:
#   1. Local development environment (docker-compose mounts the source code directly)
#   2. Multi-stage build in a project Dockerfile: use dev as the builder to produce vendor,
#      then COPY --from=build into the production image to keep it minimal

FROM ghcr.io/airygen/php:8.4-swoole-1.0.0

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install development tools and xdebug
RUN set -xe && \
  apk add --no-cache --virtual .phpize-deps ${PHPIZE_DEPS} linux-headers \
  && \
  apk add --no-cache \
  make \
  vim \
  nodejs \
  bash \
  npm \
  python3 \
  py3-pip \
  && \
  MAKEFLAGS="-j$(getconf _NPROCESSORS_ONLN)" pecl install \
  xdebug \
  && \
  docker-php-ext-enable \
  xdebug \
  && \
  apk del --no-cache .phpize-deps \
  && \
  apk cache clean
