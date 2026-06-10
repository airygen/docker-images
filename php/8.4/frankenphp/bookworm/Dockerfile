# Release tag: php-8.4-frankenphp-bookworm-<x.y.z>
# Output image: ghcr.io/airygen/php:8.4-frankenphp-bookworm-<x.y.z>
# Versioning rules:
#   MAJOR  PHP major change, FrankenPHP major bump, extension removal, breaking ini change
#   MINOR  New extension, FrankenPHP minor bump, new optional settings
#   PATCH  Extension/FrankenPHP patch bump, bug fix, default ini tweak
# See README.md in the project root for details
#
# NOTE: This is the Debian (glibc) variant of the FrankenPHP image. FrankenPHP runs
# a ZTS (multi-threaded) PHP build, and musl's small default thread-stack size on
# Alpine can segfault under worker/Octane mode. glibc avoids that, so this variant is
# the more stable choice for production at the cost of a larger image. The default
# `frankenphp` (Alpine) image remains available for size-sensitive use.
#
# Intended to run via Laravel Octane: `php artisan octane:start --server=frankenphp`.
# Octane generates and manages its own Caddyfile (host/port come from its CLI
# flags), so this base image bakes in no Caddyfile and no SERVER_NAME.

FROM dunglas/frankenphp:1-php8.4-bookworm
LABEL org.opencontainers.image.source=https://github.com/airygen/docker-images

# install-php-extensions (bundled in the FrankenPHP image) pulls build deps,
# compiles, configures (e.g. gd with jpeg/freetype/webp), and cleans up.
# opcache is already built in — we only enable it for CLI below.
RUN install-php-extensions \
  gd \
  zip \
  bcmath \
  exif \
  sockets \
  pcntl \
  pdo_mysql \
  redis-6.2.0 \
  && php -m

RUN echo "opcache.enable_cli=1" >> "$PHP_INI_DIR/conf.d/docker-php-ext-opcache.ini"

WORKDIR /var/www/html
