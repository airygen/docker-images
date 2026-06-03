# airygen/docker-images

Base Docker images for PHP applications, published to GitHub Container Registry
(GHCR). Each image is built multi-arch (`amd64` + `arm64`) from an Alpine base
and ships a curated set of PHP extensions.

Registry: `ghcr.io/airygen/php`

## Available images

All images are built on **PHP 8.4 / Alpine 3.23**.

| Runtime      | Production tag           | Dev tag                      | Base                       |
| ------------ | ------------------------ | ---------------------------- | -------------------------- |
| FPM          | `8.4-fpm-<x.y.z>`        | `8.4-fpm-dev-<x.y.z>`        | `php:8.4-fpm-alpine3.23`   |
| OpenSwoole   | `8.4-swoole-<x.y.z>`     | `8.4-swoole-dev-<x.y.z>`     | `php:8.4-cli-alpine3.23`   |
| RoadRunner   | `8.4-roadrunner-<x.y.z>` | `8.4-roadrunner-dev-<x.y.z>` | `php:8.4-cli-alpine3.23`   |

Source for each lives under `php/8.4/<runtime>/` (production) and
`php/8.4/<runtime>/dev/` (development).

### Bundled PHP extensions

Shared across all runtimes: `gd` (jpeg/freetype/webp), `zip`, `bcmath`, `exif`,
`sockets`, `pdo_mysql`, `redis`, `opcache`.

Runtime-specific additions:

- **OpenSwoole** — `openswoole`, `pcntl`
- **RoadRunner** — the `rr` binary, copied from the official
  `ghcr.io/roadrunner-server/roadrunner` multi-arch image

Timezone is set to `Asia/Taipei`, and `opcache.enable_cli=1` is enabled.

## Dev images

The `*-dev` images extend each production image with tooling for local
development and CI builders:

- **composer**
- **xdebug**
- **vim**, **bash**, **make**
- **nodejs**, **npm**
- **python3**, **py3-pip** — so AI agents / tooling can run Python in the dev
  environment

### Use cases

1. **Local development** — mount your source directly via docker-compose.
2. **Multi-stage builds** — use the dev image as a `builder` stage to produce
   `vendor/`, then `COPY --from=build` into the slim production image so the
   final image stays minimal.

```dockerfile
# Build stage: full toolchain
FROM ghcr.io/airygen/php:8.4-fpm-dev-1.0.0 AS build
WORKDIR /app
COPY composer.json composer.lock ./
RUN composer install --no-dev --no-scripts --prefer-dist

# Runtime stage: slim production image
FROM ghcr.io/airygen/php:8.4-fpm-1.0.0
WORKDIR /var/www/html
COPY --from=build /app/vendor ./vendor
COPY . .
```

## Usage

```bash
docker pull ghcr.io/airygen/php:8.4-fpm-1.0.0
docker pull ghcr.io/airygen/php:8.4-swoole-1.0.0
docker pull ghcr.io/airygen/php:8.4-roadrunner-1.0.0
```

## Versioning

Each image is versioned independently using `MAJOR.MINOR.PATCH`:

- **MAJOR** — PHP/Alpine major version change, RoadRunner major bump, extension
  removal, or breaking ini change.
- **MINOR** — new extension, Alpine minor bump, RoadRunner minor bump, or new
  optional settings.
- **PATCH** — extension/RoadRunner patch bump, bug fix, or default ini tweak.

## Releasing

Images are built and published by `.github/workflows/release-image.yml`, which
triggers on a published GitHub Release. The release **tag name** drives which
image is built:

```
php-<runtime-path>-<x.y.z>
```

The workflow strips the trailing `-<x.y.z>`, maps the dashes to a directory
under the repo, builds `amd64` and `arm64` in parallel, and stitches them into a
multi-arch manifest.

| Release tag                  | Build context              | Published tag                         |
| ---------------------------- | -------------------------- | ------------------------------------- |
| `php-8.4-fpm-1.0.0`          | `php/8.4/fpm`              | `ghcr.io/airygen/php:8.4-fpm-1.0.0`          |
| `php-8.4-swoole-1.0.0`       | `php/8.4/swoole`           | `ghcr.io/airygen/php:8.4-swoole-1.0.0`       |
| `php-8.4-roadrunner-1.0.0`   | `php/8.4/roadrunner`       | `ghcr.io/airygen/php:8.4-roadrunner-1.0.0`   |
| `php-8.4-fpm-dev-1.0.0`      | `php/8.4/fpm/dev`          | `ghcr.io/airygen/php:8.4-fpm-dev-1.0.0`      |

To cut a release, create a GitHub Release whose tag matches the runtime path
and version you want to publish.
