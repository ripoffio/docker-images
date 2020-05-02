FROM php:7.2-fpm-alpine

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
        pdo_mysql \
        mysqli \
        gd \
        zip \
        soap \
        sockets \
        bcmath \
        xsl \
        intl \
        opcache \
        exif \
    ; \
    pecl install \
        imagick \
        memcache \
        mcrypt \
        gmagick-2.0.5RC1 \
        xdebug-2.9.5 \
    ; \
    docker-php-ext-enable \
        imagick \
        memcache \
        mcrypt \
        gmagick \
        xdebug \
    ; \
    docker-php-ini-ctl disable docker-php-ext-gmagick; \
    rm -rf /tmp/pear ~/.pearrc; \
    apk del --no-network .build-deps;

# IonCube loader installation
RUN set -eux; \
    curl -s https://downloads.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz | tar -C /tmp -zx; \
    chown root:root /tmp/ioncube/ioncube_loader_lin_7.2.so; \
    mv -v /tmp/ioncube/ioncube_loader_lin_7.2.so "$(php -d 'display_errors=stderr' -r 'echo ini_get("extension_dir");')"; \
    docker-php-ext-enable --ini-name 00-ioncube.ini ioncube_loader_lin_7.2; \
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
