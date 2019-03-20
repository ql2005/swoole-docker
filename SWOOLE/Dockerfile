# sd-swoole
FROM php:7.1-cli-alpine3.9
MAINTAINER Link Qiu mynameisql@gmail.com

ENV SWOOLE_VERSION=4.2.9 \
	PHPIZE_DEPS="ca-certificates autoconf dpkg-dev dpkg file g++ gcc libc-dev make php7-dev php7-pear pkgconf re2c pcre-dev zlib-dev wget curl tar xz unzip git"


# 设置时区
ARG timezone
ENV TIMEZONE=${timezone:-"Asia/Shanghai"}


# 构建swoole环境
RUN set -ex \
    # change apk source repo
    && sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/' /etc/apk/repositories \
    && apk update \
    && apk add --no-cache libstdc++ openssl \
	&& apk add --no-cache --virtual .build-deps $PHPIZE_DEPS libaio-dev openssl-dev \
	&& apk add --no-cache \
    bash \
    libressl \
	vim \
	iproute2 \
	libjpeg-turbo-dev \
	libwebp-dev \
	libmcrypt-dev \
	libmcrypt \
	libpng \
	libpng-dev \
	--no-install-recommends \
    && pecl install mongodb \
	&& docker-php-ext-configure gd \
	&& docker-php-ext-install -j$(nproc) iconv mcrypt zip opcache bcmath pdo_mysql gd \
	&& docker-php-ext-enable gd \
	&& cd /home && rm -rf temp && mkdir temp && cd temp \
	&& wget https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz \
	https://github.com/phpredis/phpredis/archive/3.1.3.tar.gz \
	https://github.com/arnaud-lb/php-inotify/archive/2.0.0.tar.gz \
	# https://github.com/mongodb/mongo-php-driver-legacy/archive/1.6.16.tar.gz \
	https://github.com/tideways/php-xhprof-extension/archive/v4.1.7.tar.gz \
	# && git clone https://github.com/swoole/swoole-src.git \
	&& tar -xzvf 3.1.3.tar.gz \
	&& tar -xzvf v${SWOOLE_VERSION}.tar.gz \
	&& tar -xzvf 2.0.0.tar.gz \
	# && tar -xzvf 1.6.16.tar.gz \
	&& tar -xzvf v4.1.7.tar.gz \
	&& cd /home/temp/swoole-src-${SWOOLE_VERSION} \
	# && cd /home/temp/swoole-src \
	&& phpize && ./configure --enable-async-redis --enable-openssl \
	&& make -j$(nproc) && make install \
	&& cd /home/temp/phpredis-3.1.3 \
	&& phpize && ./configure \
	&& make && make install \
	&& cd /home/temp/php-inotify-2.0.0 \
	&& phpize && ./configure \
	&& make && make install \
	# && cd /home/temp/mongo-php-driver-legacy-1.6.16 \
	&& phpize && ./configure --with-php-config=php-config \
	&& make && make install \
	&& cd /home/temp/php-xhprof-extension-4.1.7 \
	&& phpize && ./configure \
	&& make && make install \
	# ---------- echo extension -------
	&& cd /usr/local/etc/php/conf.d/ \
	&& echo extension=redis.so>redis.ini \
	&& echo extension=inotify.so>inotify.ini \
	&& echo extension=mongodb.so>mongodb.ini \
	&& echo extension=tideways.so>tideways.ini \
	&& echo extension=swoole.so>swoole.ini \
	# ---------- clear works ----------
	&& rm -rf /home/temp \
	&& apk del --purge *-dev \
	&& apk del .build-deps \
	&& rm -rf /var/cache/apk/* /tmp/* /usr/share/man

COPY ./config/* /usr/local/etc/php/conf.d/

CMD ["bin/bash"]