FROM php:7.2-fpm

WORKDIR /opt/src

# install the PHP extensions we need
RUN apt-get update \
	&& apt-get install -y build-essential libmcrypt-dev libxml2-dev imagemagick \
		libjpeg62-turbo-dev \
		libpcre3-dev \
		libpng-dev \
		libfreetype6-dev \
		libxml2-dev \
		libicu-dev \
		libzip-dev \
		default-mysql-client \
		wget \
                unzip \
                openssh-client \
                sshpass \
		libonig-dev \
		&& wget http://www.imagemagick.org/download/ImageMagick.tar.gz \
                && tar -xzvf ImageMagick.tar.gz \
		&& cd ImageMagick-* \
		&& ./configure \
		&& make \
		&& make install

RUN apt-get update && apt-get install -y iproute2

RUN rm -rf /var/lib/apt/lists/*
#RUN docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/
RUN docker-php-ext-configure gd \
        --with-gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    && NPROC=$(getconf _NPROCESSORS_ONLN || 1) \
    && docker-php-ext-install -j${NPROC} \
       iconv \
       intl \
       gd \
       mbstring \
       mysqli \
       opcache \
       bcmath \
       zip \
       pdo_mysql \
    && pecl install imagick \
    && docker-php-ext-enable imagick

#RUN docker-php-ext-install iconv intl pdo_mysql mbstring soap gd zip

RUN docker-php-source extract \
  && if [ -d "/usr/src/php/ext/mysql" ]; then docker-php-ext-install mysql; fi \
  && if [ -d "/usr/src/php/ext/mcrypt" ]; then docker-php-ext-install mcrypt; fi \
	&& if [ -d "/usr/src/php/ext/opcache" ]; then docker-php-ext-install opcache; fi \
	&& docker-php-source delete
    
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
#RUN deluser xfs \
RUN usermod -u 33 www-data \
    && groupmod -g 33 www-data

RUN /opt/src/run.sh
