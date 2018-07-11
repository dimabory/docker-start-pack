# Docker Starter Pack

This [Docker Compose] powered development environment includes:

  * PHP 7.2 FPM container with app, CLI tools, and Xdebug
  * Nginx web server, serving php app
  * MySQL database
  * Composer
  * **ELK bundle** (Elasticsearch, Logstash, Kibana)
  * **RabbitMQ** messaging broker

## Installation

Docker Platform can be installed on Windows, Mac, or Linux from [their website](https://www.docker.com/products/overview). Once installed it will run as a background process, and the versions can be checked at the command line:

```
$ docker -v
$ docker-compose -v
```

### Configuring the environment

The [.env.dist](./.env.dist) file found in the root folder should be copied to `.env`, which is ignored by Git. The local web ports default to `8080` for the app.

```
# Copy default environment settings
$ cp .env.dist .env
```

This file contains an evolving set of options for database connections, web ports, [Xdebug](https://xdebug.org/) setttings to connect your IDE, and more to come.

### Environment Status

You can list running containers by running the `docker-compose ps` command.

| Name            | Command | State | Ports                       |
| --------------- | ------- | ----- | --------------------------- |
| nginx | nginx   | Up    | 443/tcp, 0.0.0.0:80->80/tcp |
| php-fpm   | php-fpm | Up    | 0.0.0.0:9000->9000/tcp      |
| mysql   | docker-entrypoint.sh mysqld | Up    | 0.0.0.0:3307->3306/tcp      |
| composer   |  /docker-entrypoint.sh comp ... | Exit 0 |  |

## What's Running?

### PHP

PHP 7.2 FPM, based on the official [php:7.2-fpm](https://hub.docker.com/_/php/) image. The following system-wide tools are included in addition to the base packages:
 * Composer
 * GIT
 * VIM
 * etc

Additionally, PHP includes following extensions:
 * Opcache
 * MySQL
 * etc

### Nginx

Nginx 1.7 is installed with apt-get on the [debian:jessie] base image.

### Elasticsearch, Logstash, Kibana (ELK)

**Docker image** 

*elk-docker* image documentation is available [here](http://elk-docker.readthedocs.io/).

**Docs** 

> All references refer to 6.2 version.

- [Elasticsearch User Guide](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)
- [Logstash User Guide](https://www.elastic.co/guide/en/logstash/6.2/index.html)
- [Kibana User Guide](https://www.elastic.co/guide/en/kibana/6.2/index.html)

### RabbitMQ

> RabbitMQ is an open source multi-protocol messaging broker. [See docker image](https://hub.docker.com/_/rabbitmq/).

Official site with tutorials, api and full documentation is available by url: 
https://www.rabbitmq.com/.

Rabbitmq configuration can be found and changed in `/rabbitmq` folder. See `rabbitmq.config` and `rabbit.json` files, which allow to set up custom queues, exchanges, bindings and other stuff. By default it defines `logging.app` exchange and `logstash-app` queue. Be careful on changing it.

### Composer

Composer is a dependency manager written in and for PHP. 
It runs `composer install` on each container building.

Running composer command (e.g. `composer update`):
```bash
$ docker-compose run --rm --user $(id -u):$(id -g) --entrypoint "composer update" composer
```

## Misc commands

```
# Stop and remove all containers
$ docker stop $(docker ps -a -q)
$ docker rm $(docker ps -a -q)

# Delete all images
$ docker rmi $(docker images -q)

# Remove unused data
$ docker system prune
```

## Troubleshooting

> Elasticsearch uses a hybrid mmapfs / niofs directory by default to store its indices. The default operating system limits on mmap counts is likely to be too low, which may result in out of memory exceptions.

On Linux, you can increase the limits by running the following command as root:

`sysctl -w vm.max_map_count=262144`

To set this value permanently, update the `vm.max_map_count` setting in `/etc/sysctl.conf`. To verify after rebooting, run `sysctl vm.max_map_count`.

**How to run tests inside container?**

```bash
docker-compose run --rm --entrypoint "./vendor/bin/simple-phpunit" app
```

[debian:jessie]: https://hub.docker.com/_/debian/
[Docker Compose]: https://docs.docker.com/compose/