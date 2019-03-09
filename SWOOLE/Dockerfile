# sd-swoole
FROM php:7.1-cli-alpine3.9
MAINTAINER Link Qiu mynameisql@gmail.com

ENV SWOOLE_VERSION=4.2.9 \
	PHPIZE_DEPS="ca-certificates autoconf dpkg-dev dpkg file g++ gcc libc-dev make php7-dev php7-pear pkgconf re2c pcre-dev zlib-dev wget curl tar xz unzip git"


# 设置时区
# RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
# 	sh -c "echo 'Asia/Shanghai' > /etc/timezone"
ARG timezone
ENV TIMEZONE=${timezone:-"Asia/Shanghai"}
#更新apt-get源 使用163的源
# RUN mv /etc/apt/sources.list /etc/apt/sources.list.bak && \
# 	echo "deb http://mirrors.163.com/debian/ stretch main non-free contrib" >/etc/apt/sources.list && \
# 	echo "deb http://mirrors.163.com/debian/ stretch-proposed-updates main non-free contrib" >>/etc/apt/sources.list && \
# 	echo "deb-src http://mirrors.163.com/debian/ stretch main non-free contrib" >>/etc/apt/sources.list && \
# 	echo "deb-src http://mirrors.163.com/debian/ stretch-proposed-updates main non-free contrib" >>/etc/apt/sources.list

# 构建swoole环境，在这里安装了php,swoole,composer
# RUN apt-get update && apt-get install -y \
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
	# gdb \
	# valgrind \
	# procps \
	# supervisor \
	iproute2 \
	libjpeg-turbo-dev \
	libwebp-dev \
	libmcrypt-dev \
	libmcrypt \
	libpng \
	libpng-dev \
	--no-install-recommends \
	&& docker-php-ext-configure gd \
	# --with-freetype-dir=/usr/include/ \
	# --with-jpeg-dir=/usr/include/ \
	&& docker-php-ext-install -j$(nproc) iconv mcrypt zip opcache bcmath pdo_mysql gd \
	&& docker-php-ext-enable gd \
	&& cd /home && rm -rf temp && mkdir temp && cd temp \
	&& wget https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz \
	# && wget https://github.com/redis/hiredis/archive/v0.13.3.tar.gz \
	https://github.com/phpredis/phpredis/archive/3.1.3.tar.gz \
	https://github.com/arnaud-lb/php-inotify/archive/2.0.0.tar.gz \
	# https://github.com/php-ds/extension/archive/v1.2.4.tar.gz \
	# && git clone https://github.com/swoole/swoole-src.git \
	&& tar -xzvf 3.1.3.tar.gz \
	# && tar -xzvf v0.13.3.tar.gz \
	&& tar -xzvf v${SWOOLE_VERSION}.tar.gz \
	&& tar -xzvf 2.0.0.tar.gz \
	# && tar -xzvf v1.2.4.tar.gz \
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
	# && cd /home/temp/ext-ds-1.2.4 \
	# && phpize && ./configure \
	# && make && make install \
	&& cd /home/temp \
	# && php -r"copy('https://getcomposer.org/installer','composer-setup.php');" \
	# && php composer-setup.php --install-dir=/usr/bin --filename=composer \
	&& cd /usr/local/etc/php/conf.d/ \
	&& echo extension=redis.so>redis.ini \
	&& echo extension=inotify.so>inotify.ini \
	&& echo extension=swoole.so>swoole.ini \
	# && echo extension=ds.so>ds.ini \
	# && composer config -g repo.packagist composer https://packagist.phpcomposer.com \
	# && mkdir -p /var/log/supervisor \
	# ---------- clear works ----------
	&& rm -rf /home/temp \
	&& apk del --purge *-dev \
	&& apk del .build-deps \
	&& rm -rf /var/cache/apk/* /tmp/* /usr/share/man

COPY ./config/* /usr/local/etc/php/conf.d/

CMD ["bin/bash"]