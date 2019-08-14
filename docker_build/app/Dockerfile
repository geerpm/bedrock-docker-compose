FROM php:7-fpm-alpine

# env, arguments

ENV COMPOSER_ALLOW_SUPERUSER 1
ARG APP_DIR=/app/bedrock

# php ext

RUN apk add --no-cache --virtual .additional-deps $PHPIZE_DEPS \
  # memcached
  && apk add gcc libgcc zlib-dev libevent libzip-dev libxml2-dev openssl curl-dev icu-dev gettext-dev tidyhtml-dev libxslt-dev libpng-dev jpeg-dev giflib-dev imagemagick-dev \
  # additonal ext
  && docker-php-ext-install -j $(nproc) gd gettext intl iconv zip pdo_mysql bcmath sockets xmlrpc tidy xsl mysqli opcache \
  ## php: alpine iconv不具合対策入れ直し(要gcc)
  && curl -SL http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.15.tar.gz | tar -xz -C ~/ \
  && rm /usr/bin/iconv \
  && mv ~/libiconv-1.15 ~/libiconv \
  && ~/libiconv/configure --prefix=/usr/bin \
  && make && make install \
  && rm -Rf ~/libiconv \
  # imagick
  && 	pecl install imagick-3.4.4 \
  && docker-php-ext-enable imagick \
  # redis
  #&& pecl install redis-4.2.0 && docker-php-ext-enable redis \
  # last
  && docker-php-source delete \
  && apk del .additional-deps

ENV LD_PRELOAD /usr/bin/lib/preloadable_libiconv.so

# composer

ADD ./docker_build/app/composer-install.sh /app/composer-install.sh
RUN sh /app/composer-install.sh && mv composer.phar /usr/local/bin/composer
RUN composer --version
# build cacheのため
COPY ./bedrock/composer.json /tmp/composer.json
COPY ./bedrock/composer.lock /tmp/composer.lock
RUN composer install --no-dev --no-scripts --no-autoloader -d /tmp

# app setup

# workspace
ADD ./bedrock $APP_DIR
WORKDIR $APP_DIR

RUN mv -n /tmp/vendor $APP_DIR/vendor && composer dump-autoload

RUN echo $APP_VERSION

RUN chown -R www-data.www-data $APP_DIR


# install wp cli
ADD ./docker_build/app/wp-cli-2.3.0.phar ./wp-cli.phar
RUN chmod +x wp-cli.phar
RUN mv wp-cli.phar /usr/local/bin/wp
RUN wp --info

# ここでユーザが変わる
USER www-data