FROM php:7.1-fpm-alpine

ENV PHPIZE_DEPS \
  autoconf \
  cmake \
  file \
  freetype-dev \
  g++ \
  git \
  gcc \
  icu-dev \
  libc-dev \
  libjpeg-turbo-dev \
  libmcrypt-dev \
  libpng-dev \
  libxslt-dev \
  libmemcached-dev \
  make \
  pcre-dev \
  pkgconf \
  re2c

RUN set -x && \
  apk update && apk add --no-cache --update --virtual buildDeps autoconf \
    freetype \
    icu \
    libjpeg-turbo \
    libmcrypt \
    libpng \
    libxslt \
    openssh \
    openssl \
    sshpass \
    tini \
    && docker-php-ext-configure gd \
    --with-freetype-dir=/usr/include/ \
    --with-png-dir=/usr/include/ \
    --with-jpeg-dir=/usr/include/ && \
  NPROC=$(getconf _NPROCESSORS_ONLN || 1) && \
  docker-php-ext-install -j${NPROC} \
    gd \
    intl \
    mcrypt \
    mysqli \
    pdo_mysql \
    xsl \
    zip \
  && rm /var/cache/apk/*

RUN wget -c https://downloads.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz && \
    tar -xvf ioncube_loaders_lin_x86-64.tar.gz -C /usr/local/lib/php/extensions/no-debug-non-zts-20160303/ --wildcards '*.so' --strip-components 1 && \
    rm ioncube_loaders_lin_x86-64.tar.gz

COPY php.ini /usr/local/etc/php/php.ini

#COPY extensions/ioncube_loader_lin_7.1.so /usr/local/lib/php/extensions/no-debug-non-zts-20160303/ioncube_loader_lin_7.1.so

RUN chmod 755 /usr/local/lib/php/extensions/no-debug-non-zts-20160303/*.so
