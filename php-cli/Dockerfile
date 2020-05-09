FROM letsdockerize/laradock-workspace:latest-7.2

LABEL maintainer="dingbin.deng <bwin90@163.com>"

RUN sed -i "s/archive.ubuntu.com/mirrors.tencentyun.com/g" /etc/apt/sources.list && \
    rm -rf /var/lib/apt/lists/* && \
    cat /etc/apt/sources.list && \
    apt-get clean

# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

# always run apt update when start and after add new source list, then clean up at end.
RUN set -xe; \
    apt-get update -yqq && \
    pecl channel-update pecl.php.net && \
    groupadd -g ${PGID} laradock && \
    useradd -u ${PUID} -g laradock -m laradock -G docker_env && \
    usermod -p "*" laradock -s /bin/bash && \
    apt-get install -yqq \
      apt-utils \
      libzip-dev zip unzip \
      # Install the zip extension
      php7.2-zip \
      # nasm
      nasm && \
      php -m | grep -q 'zip'

# Crontab
COPY ./crontab /etc/cron.d

RUN chmod -R 644 /etc/cron.d

# Imagemagick
RUN apt-get install -y imagemagick php-imagick

# MySQL client
RUN apt-get update -yqq && apt-get -y install mysql-client

# Supervisor
RUN apt-get update -yqq && apt-get install -y supervisor
COPY supervisord.conf /etc/supervisord.conf
RUN mkdir /etc/service/supervisord
COPY supervisord.sh /etc/service/supervisord/run
COPY supervisord.d /etc/supervisord.d/
RUN chmod +x /etc/service/supervisord/run

# Ghostscript
RUN apt-get install wget \
    && wget https://sh-tools-1258944054.cos.ap-shanghai.myqcloud.com/soft/ghostscript-9.52-linux-x86_64.tgz \
    && tar zxvf ghostscript-9.52-linux-x86_64.tgz \
    && mv ghostscript-9.52-linux-x86_64/gs-952-linux-x86_64 /usr/bin/gs \
    && rm -rf ghostscript* \
    && gs -v

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog
