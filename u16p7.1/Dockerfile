FROM ubuntu:16.04

RUN apt-get update && apt-get install -y openssh-server
RUN mkdir /var/run/sshd
RUN echo 'root:docker' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

# Install nginx
RUN apt-get update \
    && apt-get install -y supervisor \
    && apt-get install -y nginx \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && echo "daemon off;" >> /etc/nginx/nginx.conf 

# Install php
RUN export LANG=C.UTF-8 && apt-get update && apt-get -y install software-properties-common && add-apt-repository ppa:ondrej/php && apt-get update
RUN apt-get -y --allow-unauthenticated install php7.1
RUN apt-get install -y --allow-unauthenticated \
    php7.1-bcmath \
    php7.1-bz2 \
    php7.1-cli \
    php7.1-common \
    php7.1-curl \
    php7.1-fpm \
    php7.1-gd \
    php7.1-imap \
    php7.1-intl \
    php7.1-json \
    php7.1-mbstring \
    php7.1-mysql \
    php7.1-opcache \
    php7.1-pgsql \
    php7.1-readline \
    php7.1-sqlite3 \
    php7.1-tidy \
    php7.1-xml \
    php7.1-zip \
    php-amqp \
    php-apcu \
    php-geoip \
    php-imagick \
    php-memcache \
    php-mongodb \
    php-oauth \
    php-smbclient \
    php-solr \
    php-ssh2 \
    php-uploadprogress \
    php-uuid \
    php-yaml \
    php-xdebug

# autostart php-fpm
RUN systemctl is-enabled php7.1-fpm && systemctl enable php7.1-fpm
RUN service php7.1-fpm start #docker exec  <containerID> service php7.1-fpm start

RUN mkdir -p /var/log/supervisor
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY default /etc/nginx/sites-available/default

EXPOSE 80 22 443 

CMD ["service" "php7.1-fpm", "start"]
CMD ["/usr/bin/supervisord"]
