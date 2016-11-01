FROM php:5.6-fpm

RUN apt-get update && apt-get install -y apt-utils re2c g++ \
zlib1g zlib1g-dbg zlib1g-dev zlibc \
git

# ldap
RUN apt install -y libldap2-dev
RUN docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu
RUN docker-php-ext-install ldap


RUN docker-php-ext-install pdo_mysql && docker-php-ext-enable pdo_mysql \
&& docker-php-ext-install zip && docker-php-ext-enable zip

# ===Install Composer=== #
RUN curl -sS https://getcomposer.org/installer | php -- \
	--install-dir=/usr/local/bin \
	--filename=composer

# imagick
RUN runtimeRequirements="libmagickwand-6.q16-dev --no-install-recommends" \
	&& apt-get update && apt-get install -y ${runtimeRequirements} \
	&& ln -s /usr/lib/x86_64-linux-gnu/ImageMagick-6.8.9/bin-Q16/MagickWand-config /usr/bin/ \
	&& pecl install imagick \
	&& echo "extension=imagick.so" > /usr/local/etc/php/conf.d/ext-imagick.ini \
	&& rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-install gettext
RUN docker-php-ext-enable gettext

COPY ./upload.ini /usr/local/etc/php/conf.d/upload.ini
COPY ./charset.ini /usr/local/etc/php/conf.d/charset.ini


# Locales
RUN apt-get update && apt-get install -y locales

RUN dpkg-reconfigure locales && locale-gen ru_RU

RUN echo 'ru_RU UTF-8' >> /etc/locale.gen \
    && locale-gen


# ===Xdebug=== #
RUN pecl install -o -f xdebug \
    && rm -rf /tmp/pear
COPY xdebug.ini /usr/local/etc/php/conf.d/

RUN docker-php-ext-enable xdebug

COPY ./timezone.ini /usr/local/etc/php/conf.d/timezone.ini

ENV PATH=$PATH:/var/www/docker/bin:/var/www/vendor/bin

RUN apt-get clean && rm -rf /var/lib/apt/lists/*
WORKDIR /var/www
