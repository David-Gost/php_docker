FROM --platform=linux/amd64 php:8.1.10RC1-fpm

ENV XDEBUG_REMOTE_PORT="9004"
ENV XDEBUG_IDKEY="docker"
# mac:docker.for.win.localhost
# windows:docker.for.win.localhost
# 通用:docker.host.internal
# 先去找hosts檔裡有沒有docker新增的host
# windows:C:\Windows\System32\drivers\etc\hosts
# Linux: etc/hosts
# Mac: /private/etc/hosts
# 以上都沒作用就用本機ip
ENV XDEBUG_CLIENT_HOST="host.docker.internal"
ENV TZ=Asia/Taipei

RUN     apt-get update -y \
        && apt-get upgrade -y
RUN     apt-get install -y \
        $PHPIZE_DEPS \
        gettext \
        busybox \
        curl \
        libicu-dev \
        g++ \
        zip \
        libzip-dev \
        openssl \
        ca-certificates
        # For php gd ext
RUN     apt-get install -y \
        libwebp-dev \
        build-essential  \
        libssl-dev  \
        zlib1g-dev  \
        libpng-dev  \
        libjpeg-dev  \
        libfreetype6-dev
        # For memcached \
RUN     apt-get install -y \
        libmemcached-dev
        # For php composer \
RUN     apt-get install -y \
        unzip \
        # For Terminal
        git \
        subversion \
        vim \
        nano

RUN     apt-get install -y \
        # For memcached
        libmemcached-dev \
        # Install required packages
        default-mysql-client \
        locales

        # For ladp
RUN     apt-get install -y \
        libldap2-dev
RUN     docker-php-ext-configure intl \
        && docker-php-ext-install intl \
        && docker-php-ext-configure mysqli \
        && docker-php-ext-install -j$(nproc) mysqli

RUN     docker-php-ext-configure gettext \
        && docker-php-ext-install -j$(nproc) gettext \
        && docker-php-ext-configure gd --with-webp=/usr/include/webp --with-jpeg=/usr/include --with-freetype=/usr/include/freetype2/ \
        && docker-php-ext-install -j$(nproc) gd exif

RUN     docker-php-ext-configure ldap \
        && docker-php-ext-install ldap \
        && docker-php-ext-configure sockets \
        && docker-php-ext-install -j$(nproc) sockets

RUN     docker-php-ext-configure pdo_mysql \
        && docker-php-ext-install -j$(nproc) pdo_mysql

RUN     docker-php-ext-configure zip \
        && docker-php-ext-install -j$(nproc) zip

RUN     pecl install xdebug \
        && docker-php-ext-enable xdebug \
        && pecl install memcached \
        && docker-php-ext-enable memcached


#調整TLS版本，sql server 對應TLS版本請參閱官網說明
#https://support.microsoft.com/zh-cn/topic/kb3135244-tls-1-2-%E5%AF%B9-microsoft-sql-server-e4472ef8-90a9-13c1-e4d8-44aad198cdbe
RUN sed -i 's/TLSv1.2/TLSv1.0/g' /etc/ssl/openssl.cnf

 # for imagick
ADD https://raw.githubusercontent.com/mlocati/docker-php-extension-installer/master/install-php-extensions /usr/local/bin/

RUN chmod uga+x /usr/local/bin/install-php-extensions && sync && \
    install-php-extensions imagick sqlsrv pdo_sqlsrv
 # For composer
RUN curl -sS https://getcomposer.org/installer | php \
&& mv composer.phar /usr/local/bin/composer

# Change TimeZone
RUN ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime && echo ${TZ} > /etc/timezone

#wkhtmltopdf&中文字型
RUN apt-get update
RUN apt-get install -y ghostscript
RUN apt-get install -y apt-utils
RUN apt-get install -y xvfb
RUN apt-get install -y fonts-wqy-microhei ttf-wqy-microhei fonts-wqy-zenhei ttf-wqy-zenhei
RUN apt-get install -y xfonts-75dpi xfonts-100dpi xfonts-scalable xfonts-cyrillic xfonts-intl-chinese xfonts-intl-chinese-big xfonts-wqy xfonts-75dpi-transcoded xfonts-100dpi-transcoded

RUN apt-get install -y wget fontconfig fontconfig-config fonts-dejavu-core libbsd0 \
libfontconfig1 libfontenc1 libfreetype6 libjpeg62-turbo libmd0 libpng16-16 \
libx11-6 libx11-data libxau6 libxcb1 libxdmcp6 libxext6 libxrender1 sensible-utils \
ucf x11-common xfonts-base xfonts-encodings xfonts-utils

RUN wget https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6-1/wkhtmltox_0.12.6-1.buster_amd64.deb

RUN dpkg -i wkhtmltox_0.12.6-1.buster_amd64.deb

# xdebug setting
RUN mkdir /var/log/php/
RUN touch /var/log/php/xdebug.log
RUN chmod 777 /var/log/php/xdebug.log
RUN echo "xdebug.mode=debug" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.var_display_max_depth = -1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.var_display_max_children = -1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.var_display_max_data = -1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.client_port = $XDEBUG_REMOTE_PORT" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.start_with_request =yes" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.idekey = $XDEBUG_IDKEY" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.client_host = $XDEBUG_CLIENT_HOST" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.log = /var/log/php/xdebug.log" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
