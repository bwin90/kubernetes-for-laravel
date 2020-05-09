FROM letsdockerize/laradock-php-fpm:latest-7.2

LABEL maintainer="dingbin.deng <bwin90@163.com>"

RUN sed -i "s/deb.debian.org/mirrors.tencentyun.com/g" /etc/apt/sources.list && \
    sed -i "s/security.debian.org/mirrors.tencentyun.com/g" /etc/apt/sources.list && \
    rm -rf /var/lib/apt/lists/* && \
    cat /etc/apt/sources.list && \
    apt-get clean

# always run apt update when start and after add new source list, then clean up at end.
RUN set -xe; \
    apt-get update -yqq && \
    pecl channel-update pecl.php.net && \
    apt-get install -yqq \
      apt-utils \
      libzip-dev zip unzip && \
      docker-php-ext-configure zip --with-libzip && \
      docker-php-ext-install bcmath && \
      docker-php-ext-install opcache && \
      docker-php-ext-install mysqli && \
      docker-php-ext-install zip && \
      php -m | grep -q 'zip'

# Copy opcache configration
COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini

###########################################################################
# MySQL Client:
###########################################################################

USER root

ARG INSTALL_MYSQL_CLIENT=false

RUN if [ ${INSTALL_MYSQL_CLIENT} = true ]; then \
    apt-get update -yqq && \
    apt-get -y install mysql-client \
;fi

COPY ./laravel.ini /usr/local/etc/php/conf.d
COPY ./xlaravel.pool.conf /usr/local/etc/php-fpm.d/

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

RUN usermod -u 1000 www-data

CMD ["php-fpm"]

EXPOSE 9000
