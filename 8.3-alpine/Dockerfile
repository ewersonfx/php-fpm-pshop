FROM php:8.3-fpm-alpine

WORKDIR /opt/src

# install the PHP extensions we need
RUN apk add --update freetype-dev libpng-dev libjpeg-turbo-dev libxml2-dev autoconf g++ imagemagick-dev libtool make icu-dev shadow \
    libmemcached-libs \
    zlib \
    openssh \
    openssl \
    sshpass \
    && docker-php-ext-configure gd \
        --with-gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    && NPROC=$(getconf _NPROCESSORS_ONLN || 1) \
    && docker-php-ext-install -j${NPROC} \
       intl \
       gd \
       mbstring \
       mysqli \
       opcache \
       bcmath \
       zip \
       pdo_mysql \
#    && pecl install imagick \
#    && docker-php-ext-enable imagick \
    && set -xe \
    && cd /tmp/ \
    && apk add --no-cache --update --virtual .phpize-deps $PHPIZE_DEPS \
    && apk add --no-cache --update --virtual .memcached-deps zlib-dev libmemcached-dev cyrus-sasl-dev \
    && pecl install imagick-3.4.4 \
# Install igbinary (memcached's deps)
    && pecl install igbinary && \
# Install memcached
    ( \
        pecl install --nobuild memcached && \
        cd "$(pecl config-get temp_dir)/memcached" && \
        phpize && \
        ./configure --enable-memcached-igbinary && \
        make -j$(nproc) && \
        make install && \
        cd /tmp/ \
    ) \
# Enable PHP extensions
    && docker-php-ext-enable igbinary memcached imagick \
    && rm -rf /tmp/* \
    && apk del .memcached-deps .phpize-deps \
    && apk del autoconf g++ libtool make \
    && rm -rf /tmp/* /var/cache/apk/*

#IONCUBE

RUN wget -c https://downloads.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz && \
    tar -xvf ioncube_loaders_lin_x86-64.tar.gz -C /usr/local/lib/php/extensions/no-debug-non-zts-20170718/ --wildcards '*.so' --strip-components 1 && \
    rm ioncube_loaders_lin_x86-64.tar.gz

RUN chmod 755 /usr/local/lib/php/extensions/no-debug-non-zts-20170718/*.so

COPY php.ini /usr/local/etc/php/php.ini

#COPY extensions/ioncube_loader_lin_7.1.so /usr/local/lib/php/extensions/no-debug-non-zts-20160303/ioncube_loader_lin_7.1.so

COPY ./run.sh /opt/src/run.sh
RUN chmod 755 /opt/src/run.sh

#USER FIX UID AND GID
RUN deluser xfs \
    && usermod -u 33 www-data \
    && groupmod -g 33 www-data

RUN /opt/src/run.sh
