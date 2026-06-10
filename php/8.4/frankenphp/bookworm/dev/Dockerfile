# Release tag: php-8.4-frankenphp-bookworm-dev-<x.y.z>
# Output image: ghcr.io/airygen/php:8.4-frankenphp-bookworm-dev-<x.y.z>
# This image adds development tools (composer, xdebug, vim, node) on top of the
# Debian (glibc) production image.
#
# Use cases:
#   1. Local development environment (docker-compose mounts the source code directly)
#   2. Multi-stage build in a project Dockerfile: use dev as the builder to produce vendor,
#      then COPY --from=build into the production image to keep it minimal

FROM ghcr.io/airygen/php:8.4-frankenphp-bookworm-1.0.0

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install development tools and xdebug + pcov
# (install-php-extensions manages its own build deps and cleanup for the extensions)
RUN set -xe && \
  install-php-extensions xdebug pcov \
  && \
  apt-get update \
  && \
  apt-get install -y --no-install-recommends \
  make \
  vim \
  nodejs \
  npm \
  python3 \
  python3-pip \
  && \
  rm -rf /var/lib/apt/lists/*
