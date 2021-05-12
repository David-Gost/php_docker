FROM php:5.6-fpm-alpine

ENV XDEBUG_REMOTE_PORT="9004"
ENV XDEBUG_IDKEY="docker"
# mac:docker.for.win.localhost
# windows:docker.for.win.localhost
# 通用:docker.host.internal
# 以上都沒作用就用本機ip
ENV XDEBUG_CLIENT_HOST="192.168.1.122"

RUN     apk update \
        && apk upgrade \ 
        && apk add --update --no-cache \
        $PHPIZE_DEPS \
        gettext-dev \
        busybox-extras \
        libmcrypt-dev \
        # For php gd ext
        libwebp-dev \
        freetype-dev \
		libjpeg-turbo-dev \
		libjpeg-turbo \
        libpng-dev \
        jpeg-dev \
		icu-dev \
		zlib-dev \
        # For php zip ext
        zlib-dev \
        libzip-dev \
        # For memcached
        libmemcached-dev \
        # Install required packages
        mysql-client \
        # For php composer
        unzip \
        # For Terminal
        git \
        subversion \
        vim \
        # For ladp
        ldb-dev \
        libldap \
        && apk add --update --no-cache --virtual .docker-php-ldap-dependancies \
        openldap-dev  \
        # && rm -rf /var/cache/apk/*  \
    && docker-php-ext-configure mcrypt \
    && docker-php-ext-install mcrypt \    
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \    
    && docker-php-ext-configure mysqli \
    && docker-php-ext-install -j$(nproc) mysqli \
    && docker-php-ext-configure gettext \
    && docker-php-ext-install -j$(nproc) gettext \
    && docker-php-ext-configure gd --with-webp=/usr/include/webp --with-jpeg=/usr/include --with-freetype=/usr/include/freetype2/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-configure ldap \
    && docker-php-ext-install ldap \
    && apk del .docker-php-ldap-dependancies \
    && docker-php-ext-configure sockets \
    && docker-php-ext-install -j$(nproc) sockets \
    && docker-php-ext-configure pdo_mysql \
    && docker-php-ext-install -j$(nproc) pdo_mysql \
    && docker-php-ext-configure zip \
    && docker-php-ext-install -j$(nproc) zip \
    && pecl install xdebug-2.5.0 \
    && docker-php-ext-enable xdebug \
    && pecl install memcached-2.2.0 \
    && docker-php-ext-enable memcached \
 # for imagick
 && set -xe \
 && apk add --no-cache --virtual .build-deps \
 && apk add --no-cache bash imagemagick-dev \
 && git clone https://github.com/Imagick/imagick \
 && cd imagick \
 && git checkout master && git pull \
 && phpize && ./configure && make && make install \
 && cd .. && rm -Rf imagick \
 && docker-php-ext-enable imagick \
 && apk del .build-deps \
 && rm -rf /tmp/* /var/cache/apk/* \
 # For composer
&& curl -sS https://getcomposer.org/installer | php \
&& mv composer.phar /usr/local/bin/composer \
 # For time zone
# Install base packages
&& apk add ca-certificates && update-ca-certificates \
# Change TimeZone
&& apk add --update tzdata \
# Clean APK cache
&& rm -rf /var/cache/apk/* 
# xdebug setting
RUN mkdir /var/log/php/ 
RUN touch /var/log/php/xdebug.log 
RUN chmod 777 /var/log/php/xdebug.log
RUN echo "xdebug.remote_enable = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.remote_handler = dbgp" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.remote_port = $XDEBUG_REMOTE_PORT" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.idekey = $XDEBUG_IDKEY" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.remote_host = $XDEBUG_CLIENT_HOST" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.remote_log = /var/log/php/xdebug.log" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini 
