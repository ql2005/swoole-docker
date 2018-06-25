# sd-swoole
FROM php:7.1-cli
MAINTAINER Jincheng Zhang 896369042@qq.com link qiu mynameisql@gmail.com

ENV SWOOLE_VERSION 4.0.1

# 构建swoole环境，在这里安装了php,swoole,composer
RUN apt-get update && apt-get install -y \
	zlib1g-dev \
	vim \
	libssl-dev \
	unzip \
	wget \
	git \
	make \
	procps \
	iproute2 \
	supervisor \
	libfreetype6-dev \
	#libjpeg-dev \
	libjpeg62-turbo-dev \
	libmcrypt-dev \
	libpng-dev \
	--no-install-recommends \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
	&& docker-php-ext-install -j$(nproc) iconv mcrypt zip opcache bcmath pdo_mysql gd \
	&& docker-php-ext-enable gd \
	&& cd /home && rm -rf temp && mkdir temp && cd temp \
	&& wget https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz \
	&& wget https://github.com/redis/hiredis/archive/v0.13.3.tar.gz \
	https://github.com/phpredis/phpredis/archive/3.1.3.tar.gz \
	https://github.com/arnaud-lb/php-inotify/archive/2.0.0.tar.gz \
	https://github.com/php-ds/extension/archive/v1.2.4.tar.gz \
	# && git clone https://github.com/swoole/swoole-src.git \
	&& tar -xzvf 3.1.3.tar.gz \
	&& tar -xzvf v0.13.3.tar.gz \
	&& tar -xzvf v${SWOOLE_VERSION}.tar.gz \
	&& tar -xzvf 2.0.0.tar.gz \
	&& tar -xzvf v1.2.4.tar.gz \
	&& cd /home/temp/hiredis-0.13.3 \
	&& make -j && make install && ldconfig \
	&& cd /home/temp/swoole-src-${SWOOLE_VERSION} \
	# && cd /home/temp/swoole-src \
	&& phpize && ./configure --enable-async-redis --enable-openssl && make \
	&& make install \
	&& cd /home/temp/phpredis-3.1.3 \
	&& phpize && ./configure \
	&& make && make install \
	&& cd /home/temp/php-inotify-2.0.0 \
	&& phpize && ./configure \
	&& make && make install \
	&& cd /home/temp/extension-1.2.4 \
	&& phpize && ./configure \
	&& make && make install \
	&& cd /home/temp \
	&& php -r"copy('https://getcomposer.org/installer','composer-setup.php');" \
	&& php composer-setup.php --install-dir=/usr/bin --filename=composer \
	&& rm -rf /home/temp \
	&& cd /usr/local/etc/php/conf.d/ \
	&& echo extension=redis.so>redis.ini \
	&& echo extension=inotify.so>inotify.ini \
	&& echo extension=swoole.so>swoole.ini \
	&& echo extension=ds.so>ds.ini \
	&& composer config -g repo.packagist composer https://packagist.phpcomposer.com \
	&& mkdir -p /var/log/supervisor \
	&& apt-get autoclean \
    && apt-get autoremove \
    && rm -rf /var/lib/apt/lists/*

COPY ./config/* /usr/local/etc/php/conf.d/

CMD ["bin/bash"]