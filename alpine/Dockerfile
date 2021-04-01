FROM php:7.2-fpm-alpine

WORKDIR /opt/src

# install the PHP extensions we need
RUN apk add --update freetype-dev libpng-dev libjpeg-turbo-dev libxml2-dev autoconf g++ imagemagick-dev libtool make icu-dev shadow \
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
    && pecl install imagick \
    && docker-php-ext-enable imagick \
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
