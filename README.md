# Symfony & Docker

A template for new Symfony applications using Docker.

## Default Stack

- PHP-FPM
- MySQL
- NGINX

## The `devops/` Directory

The `devops` directory holds all DevOps related concepts, thus separately from the application concern.

ℹ️ Don't mix&match `.env` files; any concern could rely on a different parsing technique ([ref](https://github.com/symfony/recipes/pull/487))

### `devops/environment/`

The `environment` directory holds all available application staging environments, each environment containing a
`docker-compose.yaml` file at least. By default environments inherit from the `base` directory, which is not an
environment on itself.

To customize a staging environment use:

```bash
cp -n devops/environment/dev/.env.dist devops/environment/dev/.env
```

To create a new staging environment (e.g. `prod`) use:

```bash
cp -R devops/environment/dev devops/environment/prod
```

⚠️ Never commit secret values in `.env.dist` for non-dev environments

ℹ️ Consider standard "DTAP" environments (_Development, Testing, Acceptance and Production_) a best practice

> This template by default assumes `dev` and `prod` for respectively development and production

### `devops/docker/`

The `docker` directory holds all available application services. Each directory represents a single service, containing
a `Dockerfile` at least.

A `setup.sh` binary can be defined to setup the host system before building the service (e.g. pull in external sources).
It is automatically invoked during build.

Setup files can leverage staging environment variables sourced from `devops/environment/<staging-env>/.env`. The current
staging environment is identified by `$STAGING_ENV`.

Services (`Dockerfile`) can obtain the current staging environment from a build argument, e.g. `ARG staging_env`.

ℹ️ Consider a single service per concept, to be used across all staging environments, a best practice

## 0. Create Application

Bootstrap the initial skeleton first:

```bash
# latest stable
./install.sh

# specify version
SF=x.y ./install.sh
SF=x.y.z ./install.sh

# specify stability
STABILITY=dev ./install.sh

# skip initial commit
NO_COMMIT=1 ./install.sh
```

⚠️ Cleanup the installer:

```bash
rm install.sh
```

And done. Continue to [step 4](#4-run-application) (start from [step 1](#1-build-application) after a fresh clone).

## 1. Build Application

To create a default development build use:

```bash
make build
```

Build the application for a specific staging environment using:

```bash
STAGING_ENV=prod ARGS='--no-cache --build-arg foo=bar' make build
```

## 2. Start Application

To start the application locally in development mode use:

```bash
make start
```

Consider a restart to have fresh containers once started:

```bash
make restart
```

## 3. Install Application

Install the application using:

```bash
make install
```

Consider a refresh (build/start/install) to install the application from scratch:

```bash
make refresh
```

## 4. Run Application

Visit the application at: http://localhost:8080 (`NGINX_PORT`)

Start a shell using:

```bash
make shell

# enter web service 
SERVICE=web make shell
```

Start a MySQL client using:

```bash
make mysql

# enter test database
SERVICE=db-test make mysql
```

# Miscellaneous

## One-Off Commands

```bash
sh -c "$(make exec) app ls"
```

Alternatively, use `make run` to create a temporary container and run as `root` user by default.

```bash
sh -c "$(make run) --no-deps app whoami"
```

## Normalization

Normalize source files (e.g. `composer.json`) using:

```bash
make normalize
```

## Debug

Display current docker-compose configuration and/or its images using:

```bash
make composed-config
make composed-images
```

Follow service logs:

```bash
make log
```

## Verify Symfony Requirements

After any build it might be considered to verify if Symfony requirements are (still) met using:

```bash
make requirement-check
```

# Contributing

See [`CONTRIBUTING.md`](CONTRIBUTING.md)

# References

- https://github.com/api-platform/api-platform/blob/master/api/Dockerfile
- https://github.com/jakzal/docker-symfony-intl/blob/master/Dockerfile-intl
