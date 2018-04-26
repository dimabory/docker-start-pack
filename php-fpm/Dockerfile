FROM php:7.2-fpm

# update and install libs
RUN apt-get -qq update && apt-get -qq install -y \
        git \
        libcurl4-gnutls-dev \
        libfreetype6-dev \
        libgmp-dev \
        libicu-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpq-dev \
        vim \
        wget \
        unzip

# link gmp
RUN ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h

# set timezone
RUN ln -fs /usr/share/zoneinfo/US/Pacific-New /etc/localtime && dpkg-reconfigure -f noninteractive tzdata
RUN "date"

# install php extensions
#RUN docker-php-ext-install -j$(nproc) bcmath iconv mcrypt
#RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/
RUN docker-php-ext-install -j$(nproc) gd gmp intl opcache sockets zip curl pdo_mysql

# add conf.d/php.ini
ADD ./config/php.ini /usr/local/etc/php/conf.d/php.ini

# install php data structures
RUN pecl install ds
RUN docker-php-ext-enable ds

# APCu
RUN pecl install apcu
RUN docker-php-ext-enable apcu

# sodium
#RUN pecl install libsodium

# ImageMagick
RUN apt-get update && apt-get install -y libmagickwand-6.q16-dev --no-install-recommends \
&& ln -s /usr/lib/x86_64-linux-gnu/ImageMagick-6.8.9/bin-Q16/MagickWand-config /usr/bin \
&& pecl install imagick \
&& echo "extension=imagick.so" > /usr/local/etc/php/conf.d/ext-imagick.ini

# install xdebug
RUN pecl install xdebug
RUN docker-php-ext-enable xdebug
ADD ./config/php-xdebug.ini /usr/local/etc/php/conf.d/php-xdebug.ini

# set up workdir
# WORKDIR /