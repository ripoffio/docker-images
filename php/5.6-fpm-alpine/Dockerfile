FROM php:5.6-fpm-alpine

WORKDIR /var/www/html

COPY docker-php-ini-ctl docker-entrypoint /usr/local/bin/

RUN set -eux; \
    cp -v "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"; \
    apk add --no-cache \
        imagemagick \
        imagemagick-libs \
        graphicsmagick \
        zlib \
        libpng \
        libjpeg-turbo \
        freetype \
        libzip \
        libbz2 \
        libxml2 \
        libxslt \
        icu \
        icu-libs \
        libmcrypt \
    ; \
    apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        imagemagick-dev \
        graphicsmagick-dev \
        libtool \
        zlib-dev \
        bzip2-dev \
        libpng-dev \
        libjpeg-turbo-dev \
        freetype-dev \
        libzip-dev \
        libxml2-dev \
        libxslt-dev \
        icu-dev \
        libmcrypt-dev \
    ; \
    docker-php-ext-configure gd --with-jpeg-dir=/usr --with-freetype-dir=/usr; \
    docker-php-ext-install \
        calendar \
        hash \
        pdo_mysql \
        mysqli \
        gd \
        zip \
        bz2 \
        soap \
        sockets \
        bcmath \
        xsl \
        intl \
        opcache \
        exif \
        mcrypt \
    ; \
    pecl install \
        imagick \
        gmagick-1.1.7RC3 \
        xdebug-2.5.5 \
        memcache-3.0.8 \
    ; \
    docker-php-ext-enable \
        bz2 \
        hash \
        calendar \
        imagick \
        gmagick \
        xdebug \
    ; \
    docker-php-ini-ctl disable docker-php-ext-gmagick; \
    rm -rf /tmp/pear ~/.pearrc; \
    apk del --no-network .build-deps;

# Replace faulty memcache PHP library (apply patch and rebuild)
RUN set -eux; \
    apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        zlib-dev \
    ; \
    curl -s http://pecl.php.net/get/memcache-3.0.8.tgz | tar -C /tmp -zx; \
    cd /tmp/memcache-3.0.8; \
    curl -o memcache-faulty-inline.patch https://gitlab.alpinelinux.org/alpine/aports/-/raw/0aa4af1a9360dbbcccf3327bc451b0813a60d42b/main/php-memcache/memcache-faulty-inline.patch; \
    patch -p1 -i memcache-faulty-inline.patch; \
    phpize; \
    ./configure; \
    make; \
    make install; \
    rm -rf /tmp/memcache-3.0.8; \
    docker-php-ext-enable memcache; \
    apk del --no-network .build-deps;

# IonCube loader installation
RUN set -eux; \
    curl -s https://downloads.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz | tar -C /tmp -zx; \
    chown root:root /tmp/ioncube/ioncube_loader_lin_5.6.so; \
    mv -v /tmp/ioncube/ioncube_loader_lin_5.6.so "$(php -d 'display_errors=stderr' -r 'echo ini_get("extension_dir");')"; \
    docker-php-ext-enable --ini-name 00-ioncube.ini ioncube_loader_lin_5.6; \
    docker-php-ini-ctl disable 00-ioncube; \
    rm -rf /tmp/ioncube;

# Xdebug configuration
RUN set -eux; \
    docker-php-ini-ctl disable docker-php-ext-xdebug; \
    { \
        echo '[xdebug]'; \
        echo 'xdebug.remote_autostart=On'; \
        echo 'xdebug.remote_enable=On'; \
        echo "xdebug.remote_port=9000"; \
        echo 'xdebug.remote_host=127.0.0.1'; \
        echo 'xdebug.idekey=PHPSTORM'; \
    } | tee -a "$PHP_INI_DIR/conf.d/docker-php-ext-xdebug.ini-disabled";

# Composer installation
COPY --from=composer /usr/bin/composer /usr/local/bin/composer

ENTRYPOINT ["docker-entrypoint"]

CMD ["php-fpm"]
