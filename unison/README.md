# Quick reference

- **Maintained by**: [the RIPOFF.IO](https://github.com/ripoffio)

# Supported tags and respective `Dockerfile` links

- [2.51.2-alpine](https://github.com/ripoffio/docker-images/blob/master/unison/2.51.2-alpine/Dockerfile)
- [2.48.4-alpine](https://github.com/ripoffio/docker-images/blob/master/unison/2.48.4-alpine/Dockerfile)

# Quick reference (cont.)

- **Where to file issues**: [https://github.com/ripoffio/docker-images/issues](https://github.com/ripoffio/docker-images/issues)
- **Source of this description**: [image directory](https://github.com/ripoffio/docker-images/tree/master/unison)

# How to use this image

### macOS LAMP server with zero unison configuration example

This configuration will sync mounted directories inside the unison container, so no need to start sync process on a local machine. 

Images can be used as base images for example for a LAMP [alpine-based](https://hub.docker.com/_/alpine) docker server on macOS. Please note that the [Apache httpd](https://hub.docker.com/_/httpd) service must be configured to run as www-data user and document root must be set to `/var/www/html`.  

Docker compose file (`docker-compose.yml`) of a LAMP docker server example:
```yaml
version: "3.7"

services:

  unison-html:
    build: ./unison-html
    volumes:
      - ./app:/mnt/data
      - data-html:/var/www/html
    restart: on-failure

  httpd:
    image: httpd:2.4-alpine
    ports:
      - 80:80
    volumes:
      - data-html:/var/www/html
  
  php:
    image: ripoffio/php:7.2-fpm-alpine
    volumes:
      - data-html:/var/www/html
    environment:
      PHP_OPCACHE_DISABLE: 0
      PHP_XDEBUG_ENABLE: 1
      PHP_IONCUBE_ENABLE: 1
      PHP_GMAGICK_ENABLE: 0
      XDEBUG_CONFIG: "remote_host=host.docker.internal"

  ... database configuration, etc ...

volumes:
  data-html:
```

Unison docker file (`./unison-html/Dockerfile`):

```dockerfile
FROM ripoffio/unison:2.51.2-alpine

RUN set -x \
	&& addgroup -g 82 -S www-data \
	&& adduser -u 82 -D -S -G www-data www-data

ENV UNISON_SRC /mnt/data
ENV UNISON_DEST /var/www/html

RUN set -eux; \
	[ ! -d ${UNISON_DEST} ]; \
	mkdir -p ${UNISON_DEST}; \
	chown www-data:www-data ${UNISON_DEST}; \
	chmod 777 ${UNISON_DEST}; \
	\
   	[ ! -d ${UNISON_SRC} ]; \
    mkdir -p ${UNISON_SRC}; \
    chown www-data:www-data ${UNISON_SRC}; \
    chmod 777 ${UNISON_SRC}; \
	\
    mkdir -p /unison; \
    chown www-data:www-data /unison; \
    ln -s /unison /home/www-data/.unison;

COPY unison-sync /usr/local/bin/unison-sync

VOLUME /unison

WORKDIR ${UNISON_DEST}

USER www-data

CMD ["unison-sync"]
```

Unison CMD shell script (`unison-sync`):

```shell script
#!/bin/sh
set -e

exec unison -root="${UNISON_SRC}" \
            -root="${UNISON_DEST}" \
            -prefer="${UNISON_SRC}" \
            -auto \
            -batch \
            -fastcheck=true \
            -contactquietly \
            -repeat=watch \
            -confirmbigdel=false \
            -log=false \
            "$@"
```

# What is Unison?

Unison is a file-synchronization tool for OSX, Unix, and Windows. It allows two replicas of a collection of files and directories to be stored on different hosts (or different disks on the same host), modified separately, and then brought up to date by propagating the changes in each replica to the other.

> [www.cis.upenn.edu/~bcpierce/unison/](https://www.cis.upenn.edu/~bcpierce/unison/)

![logo](https://www.cis.upenn.edu/~bcpierce/unison/Unison.gif)

# License

View [license information](https://github.com/ripoffio/docker-images/blob/master/LICENSE) for the software contained in this image.

View Unison [license information](https://github.com/bcpierce00/unison/blob/master/LICENSE) at the official GitHub repository.

As with all Docker images, these likely also contain other software which may be under other licenses (such as Bash, etc from the base distribution, along with any direct or indirect dependencies of the primary software being contained).

Some additional license information which was able to be auto-detected might be found in the [repository's unison/ directory](https://github.com/ripoffio/docker-images/tree/master/unison).

As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.
