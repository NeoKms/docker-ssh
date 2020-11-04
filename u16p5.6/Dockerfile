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
RUN apt-get -y --allow-unauthenticated install php5.6
RUN apt-get install -y --allow-unauthenticated \
    php5.6-bcmath \
    php5.6-bz2 \
    php5.6-cli \
    php5.6-common \
    php5.6-curl \
    php5.6-fpm \
    php5.6-gd \
    php5.6-imap \
    php5.6-intl \
    php5.6-json \
    php5.6-mbstring \
    php5.6-mysql \
    php5.6-opcache \
    php5.6-pgsql \
    php5.6-readline \
    php5.6-sqlite3 \
    php5.6-tidy \
    php5.6-xml \
    php5.6-zip \
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
RUN systemctl is-enabled php5.6-fpm && systemctl enable php5.6-fpm
RUN service php5.6-fpm start #docker exec  <containerID> service php5.6-fpm start

RUN mkdir -p /var/log/supervisor
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY default /etc/nginx/sites-available/default

EXPOSE 80 22 443 

CMD ["service" "php5.6-fpm", "start"]
CMD ["/usr/bin/supervisord"]
