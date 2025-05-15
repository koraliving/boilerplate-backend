# Configure

## Docker

Install docker in your system (https://docs.docker.com/engine/install/).

Open the `docker` folder in the terminal.

```sh
$ cd docker
```

Copy `env.dist` to `.env`:

```sh
$ cp env.dist .env
```

You can edit `.env` to your needs but can be left as is.

`NAME` is used to name the containers. It will be added at the beginning.
By default, the containers will be called:

```
symfony-nginx-1
symfony-php-fpm-1
```

`HOST` variable is used to create the SSL certificates adding the configured
top level domain variable `TLD` ("localhost" by default) to the `NAME`.
Add it to `/etc/hosts` in your host pointing to 127.0.0.1:

```sh
$ echo -e "\n127.0.0.1\tsymfony.localhost\n" | sudo tee -a /etc/hosts
```

You should see this in `/etc/hosts`:

```
127.0.0.1       symfony.localhost
```

Create the SSL certificate by running `mkcert.sh`:

```sh
$ ./mkcert.sh
```

Go back to the main folder:

```sh
$ cd ..
```

Build the containers:

```sh
$ UID=$(id -u) GID=$(id -g) docker compose -f docker/docker-compose.yml build
```

Start the containers:

```sh
$ UID=$(id -u) GID=$(id -g) docker compose -f docker/docker-compose.yml up
```

Open a terminal inside the PHP-FPM container:

```sh
$ docker exec --user symfony -it symfony-php-fpm-1 /bin/bash
```

## Symfony

Within PHP-FPM contaniner create a local configuration from `.env`:

```sh
$ cp .env .env.local
```

Install the vendors:

```sh
$ composer install
```

Run the Doctrine migrations:

```sh
$ php bin/console doctrine:migrations:migrate
```

Install and compile assets:

```sh
$ php bin/console assets:install
$ php bin/console importmap:install
$ php bin/console asset-map:compile
```

Clear the cache:

```sh
$ php bin/console cache:clear
```

Open https://symfony.localhost (or the domain you configured previously in `docker/.env`).

Now you are ready to start coding!
