# Serve a Laravel app with FrankenPHP on Clever Cloud

This is a simple demo of how to deploy a Laravel application served by [FrankenPHP](https://frankenphp.dev/) in a Docker container on Clever Cloud. You'll need a [Clever Cloud account](https://console.clever-cloud.com/) and [Clever Tools](https://github.com/CleverCloud/clever-tools).

## Setup Clever Tools

```bash
npm i -g clever-tools
clever login
```
## Create the local application

To create the Laravel application, you'll need [Composer](https://getcomposer.org/download), then:

```bash

composer create-project laravel/laravel frankenLaravel
cd frankenLaravel
git init
``` 

## Create and configure the Clever Cloud application

We set the port (`8080`) of FrankenPHP via `SERVER_NAME`. We also set `APP_ENV` and `APP_KEY` used by Laravel:

```bash
clever create -t docker 
clever env set APP_KEY "$(grep '^APP_KEY=' .env | cut -d '=' -f2)"
clever env set APP_ENV "production"
clever env set SERVER_NAME ":8080"
```

## Create the Dockerfile

```Bash
cat << 'EOF' > Dockerfile
FROM dunglas/frankenphp

COPY . /opt/app/
WORKDIR /opt/app/

# Install dependencies
RUN apt-get update && apt-get install -y \
    git \
    zip \
    unzip

# Install composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php composer-setup.php && \
    php -r "unlink('composer-setup.php');" && \
    php composer.phar install
EOF
```

You can also choose to download the Dockerfile from this repository:

```Bash
wget -q https://raw.githubusercontent.com/davlgd/frankenphp-laravel-demo/main/Dockerfile
```

## Git push!

```Bash
git add . && git commit -m "Initial commit"
clever deploy
clever open
```

After the deployment, you should see the Laravel application running on Clever Cloud.
