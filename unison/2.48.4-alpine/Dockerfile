FROM alpine:3.11

ARG UNISON_VER=2.48.4

RUN set -eux; \
    apk add --no-cache --virtual .build-deps \
        build-base \
        bash \
        ocaml \
        git \
        emacs \
    ; \
    wget -q -O - https://github.com/bcpierce00/unison/archive/${UNISON_VER}.tar.gz | tar -C /tmp -zx; \
    cd /tmp/unison-${UNISON_VER}/src; \
    sed -i -e 's/GLIBC_SUPPORT_INOTIFY 0/GLIBC_SUPPORT_INOTIFY 1/' fsmonitor/linux/inotify_stubs.c; \
    # Apply ocaml-4.08 patch from https://bugs.gentoo.org/709646
    wget -q -O - https://709646.bugs.gentoo.org/attachment.cgi?id=620918 | git apply; \
    make; \
    cp ./unison ./unison-fsmonitor /usr/local/bin; \
    rm -rf /tmp/unison-${UNISON_VER}; \
    apk del --no-network .build-deps; \
    unison -version;

COPY docker-entrypoint /usr/local/bin/

ENTRYPOINT ["docker-entrypoint"]

EXPOSE 5000

CMD ["unison", "-socket=5000"]

