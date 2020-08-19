# Quick reference

- **Maintained by**: [the RIPOFF.IO](https://github.com/ripoffio)

# Supported tags and respective `Dockerfile` links

- [5.6-fpm-alpine](https://github.com/ripoffio/docker-images/blob/master/php/5.6-fpm-alpine/Dockerfile)
- [7.2-fpm-alpine](https://github.com/ripoffio/docker-images/blob/master/php/7.2-fpm-alpine/Dockerfile)

# Quick reference (cont.)

- **Where to file issues**: [https://github.com/ripoffio/docker-images/issues](https://github.com/ripoffio/docker-images/issues)
- **Source of this description**: [image directory](https://github.com/ripoffio/docker-images/tree/master/php)  

# Extensions

Additional extensions and [composer](https://getcomposer.org) were added to support popular CSM and eCommerce platforms.

- `Zend OPcache` - OPcache improves PHP performance by storing precompiled script bytecode in shared memory, thereby removing the need for PHP to load and parse scripts on each request. See more details at [PHP manual page](https://www.php.net/manual/en/book.opcache.php).

- `Xdebug` - The Xdebug extension helps you debugging your script by providing a lot of valuable debug information. See more details at [PECL package page](https://pecl.php.net/package/xdebug) and [official page](https://xdebug.org).

- `ionCube PHP Loader` - ionCube PHP Loader extension allows to execute scripts protected by the ionCube PHP Encoder. See additional details at [official page](https://www.ioncube.com/loaders.php).

- `gmagick` - Provides a wrapper to the GraphicsMagick image processing library. See more details at [PECL package page](https://pecl.php.net/package/gmagick) and [PHP manual page](https://www.php.net/manual/en/book.gmagick.php).

- `imagick` - Provides a wrapper to the ImageMagick image processing library. See more details at [PECL package page](https://pecl.php.net/package/imagick) and [PHP manual page](https://www.php.net/manual/en/book.imagick.php).

- `gd` - Provides a wrapper to the LibGD image processing library. See more details at [PHP manual page](https://www.php.net/manual/en/book.image.php).

- `exif` - With the exif extension you are able to work with image meta data. For example, you may use exif functions to read meta data of pictures taken from digital cameras by working with information stored in the headers. These are commonly found in JPEG and TIFF images. See more details at [PHP manual page](https://www.php.net/manual/en/book.exif.php).

- `memcache` - Memcache module provides handy procedural and object oriented interface to memcached, highly effective caching daemon, which was especially designed to decrease database load in dynamic web applications. See more details at [PECL package page](https://pecl.php.net/package/memcache) and [PHP manual page](https://www.php.net/manual/en/intro.memcache.php).

- `mcrypt` - Provides bindings for the unmaintained libmcrypt. See more details at [PECL package page](https://pecl.php.net/package/mcrypt) and [PHP manual page](https://www.php.net/manual/en/intro.mcrypt.php).

- `pdo_mysql` - PDO_MYSQL DSN — Connecting to MySQL databases. See more details at [PHP manual page](https://www.php.net/manual/en/ref.pdo-mysql.connection).

- `mysqli` - The mysqli extension allows you to access the functionality provided by MySQL 4.1 and above. See more details at [PHP manual page](https://www.php.net/manual/en/book.mysqli.php).

- `zip` - This extension enables you to transparently read or write ZIP compressed archives and the files inside them. See more details at [PHP manual page](https://www.php.net/manual/en/intro.zip.php).

- `soap` - The SOAP extension can be used to write SOAP Servers and Clients. See more details at [PHP manual page](https://www.php.net/manual/en/book.soap.php).

- `sockets` - The socket extension implements a low-level interface to the socket communication functions based on the popular BSD sockets, providing the possibility to act as a socket server as well as a client. See more details at [PHP manual page](https://www.php.net/manual/en/book.sockets.php).

- `bcmath` - For arbitrary precision mathematics PHP offers BCMath which supports numbers of any size and precision up to 2147483647 (or 0x7FFFFFFF) decimals, if there is sufficient memory, represented as strings. See more details at [PHP manual page](https://www.php.net/manual/en/book.bc.php).

- `xsl` - The XSL extension implements the XSL standard, performing [XSLT transformations](http://www.w3.org/TR/xslt) using the [libxslt library](http://xmlsoft.org/XSLT). See more details at [PHP manual page](https://www.php.net/manual/en/book.xsl.php).

- `intl` - Internationalization extension is a wrapper for [ICU](http://www.icu-project.org) library, enabling PHP programmers to perform various locale-aware operations including but not limited to formatting, transliteration, encoding conversion, calendar operations, [UCA-conformant](http://www.unicode.org/reports/tr10) collation, locating text boundaries and working with locale identifiers, timezones and graphemes. See more details at [PHP manual page](https://www.php.net/manual/en/book.intl.php).

# How to use this image

### Compose example

```yaml
version: "3.7"

services:

  apache:
    image: httpd:2.4-alpine
    ports:
      - 80:80
    volumes:
      - html-data:/var/www/html
      - ./httpd-extra.conf:/usr/local/apache2/conf/extra/httpd-extra.conf

  php:
    image: ripoffio/php:7.2-fpm-alpine
    volumes:
      - html-data:/var/www/html
      - ./php-development.ini:/usr/local/etc/php/conf.d/php-development.ini
    environment:
      PHP_OPCACHE_DISABLE: 0
      PHP_XDEBUG_ENABLE: 1
      PHP_IONCUBE_ENABLE: 1
      PHP_GMAGICK_ENABLE: 0
      XDEBUG_CONFIG: "remote_host=host.docker.internal"
``` 

### Other usage cases and examples

Please see the official [docker PHP images](https://hub.docker.com/_/php/) page section "How to use this image".

## Environment Variables

When you start the image, you can adjust the configuration of the image instance by passing one or more environment variables on the `docker run` command line.

### `PHP_OPCACHE_DISABLE`

This is optional variable. If set to `1` the [Zend OPcache](https://www.php.net/manual/en/book.opcache.php) extension will be disabled (enabled by default).

### `PHP_XDEBUG_ENABLE`

This is optional variable. If set to `1` the [Xdebug](https://pecl.php.net/package/xdebug) extension will be enabled (disabled by default).

### `PHP_IONCUBE_ENABLE`

This is optional variable. If set to `1` the [ionCube PHP Loader](https://www.ioncube.com/loaders.php) extension will be enabled (disabled by default).

### `PHP_GMAGICK_ENABLE`

This is optional variable. If set to `1` the [gmagick](https://pecl.php.net/package/gmagick) image processing extension will be used instead of [imagick](https://pecl.php.net/package/imagick).

# What is PHP?

PHP is a server-side scripting language designed for web development, but which can also be used as a general-purpose programming language. PHP can be added to straight HTML or it can be used with a variety of templating engines and web frameworks. PHP code is usually processed by an interpreter, which is either implemented as a native module on the web-server or as a common gateway interface (CGI).

> [wikipedia.org/wiki/PHP](https://en.wikipedia.org/wiki/PHP)

![logo](https://raw.githubusercontent.com/docker-library/docs/01c12653951b2fe592c1f93a13b4e289ada0e3a1/php/logo.png)

# What is Composer?

Composer is a tool for dependency management in PHP, written in PHP. It allows you to declare the libraries your project depends on and it will manage (install/update) them for you.

You can read more about Composer in the [official documentation](https://getcomposer.org/doc/).

![logo](https://raw.githubusercontent.com/docker-library/docs/58f7363e6cfa78f8cd54af16eab51c63c1232002/composer/logo.png)

# License

View [license information](https://github.com/ripoffio/docker-images/blob/master/LICENSE) for the software contained in this image.

View PHP [license information](https://www.php.net/license/).

View Composer [license information](https://github.com/composer/composer/blob/master/LICENSE).

As with all Docker images, these likely also contain other software which may be under other licenses (such as Bash, etc from the base distribution, along with any direct or indirect dependencies of the primary software being contained).

Some additional license information which was able to be auto-detected might be found in the [repository's php/ directory](https://github.com/ripoffio/docker-images/tree/master/php).

As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.
