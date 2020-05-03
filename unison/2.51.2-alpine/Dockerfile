FROM alpine:3.11

ARG UNISON_VER=2.51.2

RUN set -eux; \
    apk add --no-cache --virtual .build-deps \
        build-base \
        bash \
        ocaml \
        git \
    ; \
    wget -q -O - https://github.com/bcpierce00/unison/archive/v${UNISON_VER}.tar.gz | tar -C /tmp -zx; \
    cd /tmp/unison-${UNISON_VER}; \
    sed -i -e 's/GLIBC_SUPPORT_INOTIFY 0/GLIBC_SUPPORT_INOTIFY 1/' src/fsmonitor/linux/inotify_stubs.c; \
    wget -q -O - https://git.archlinux.org/svntogit/packages.git/plain/trunk/4.08-compatibility.patch?h=packages/unison | git apply; \
    make; \
    cp src/unison src/unison-fsmonitor /usr/local/bin; \
    rm -rf /tmp/unison-${UNISON_VER}; \
    apk del --no-network .build-deps; \
    unison -version;

COPY docker-entrypoint /usr/local/bin/

ENTRYPOINT ["docker-entrypoint"]

EXPOSE 5000

CMD ["unison", "-socket=5000"]

