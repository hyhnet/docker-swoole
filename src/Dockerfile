FROM php:7.1-alpine

MAINTAINER hyhnet <cyrusyun@qq.com>

ENV LANG zh_CN.UTF-8

ENV LC_CTYPE zh_CN.UTF-8

ENV PHP_EXT_SWOOLE=4.2.12

RUN echo "http://mirrors.aliyun.com/alpine/v3.8/main" > /etc/apk/repositories \
    && echo "http://mirrors.aliyun.com/alpine/v3.8/community" >> /etc/apk/repositories \
    && apk update upgrade \
    && apk add --no-cache procps unzip curl bash tzdata \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone

RUN apk add libxml2-dev
RUN apk add libpng
RUN apk add libpng-dev
RUN apk add pcre-dev
RUN apk add git
RUN apk add openssl
RUN apk add openssl-dev
RUN apk add nghttp2-dev

RUN docker-php-ext-install pdo_mysql

RUN docker-php-ext-install soap

RUN docker-php-ext-install gd

RUN docker-php-ext-install zip

RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://packagist.phpcomposer.com \
    && composer self-update --clean-backups \
    && set -xe \
    && apk add --no-cache --update --virtual .phpize-deps $PHPIZE_DEPS \
    && pecl install redis \
    && docker-php-ext-enable redis \
    && pecl clear-cache \
    && apk add hiredis-dev \
    && wget https://github.com/swoole/swoole-src/archive/v$PHP_EXT_SWOOLE.tar.gz -O swoole.tar.gz \
    && mkdir -p swoole \
    && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
    && rm swoole.tar.gz \
    && ( \
        cd swoole \
        && phpize \
        && ./configure --enable-mysqlnd --enable-openssl \
        && make -j$(nproc) \
        && make install \
    ) \
    && docker-php-ext-enable swoole \
    && rm -r swoole \
    && rm -rf /usr/share/php \
    && rm -rf /tmp/* \
    && apk del  .phpize-deps \
    && rm -rf /var/cache/apk/*
