FROM php:5.6-apache

RUN apt-get update && apt-get install openssl -y
RUN apt-get update && apt-get install -y libpng-dev libjpeg-dev libcurl3-dev mysql-client vim git wget
RUN docker-php-ext-configure gd \
        --with-png-dir=/usr/lib/ \
        --with-jpeg-dir=/usr/lib/ \
        --with-gd

RUN docker-php-ext-install pdo pdo_mysql mysql mbstring bcmath fileinfo gd
RUN yes | pecl install xdebug-2.5.5 \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_host={{ default .Env.DEBUG_HOST \"localhost\" }}" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_port={{ default .Env.DEBUG_PORT \"localhost\" }}" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_handler=dbgp" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_mode=req" >> /usr/local/etc/php/conf.d/xdebug.ini  \
    && echo "xdebug.remote_autostart=on" >> /usr/local/etc/php/conf.d/xdebug.ini 
RUN docker-php-ext-enable xdebug

ENV DOCKERIZE_VERSION v0.6.1
RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz

RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer

RUN a2enmod rewrite
RUN a2enmod headers
RUN a2enmod expires

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

EXPOSE 80 9000

CMD ["dockerize", "-template", "/usr/local/etc/php/conf.d/xdebug.ini:/usr/local/etc/php/conf.d/xdebug.ini", "/usr/sbin/apache2ctl", "-D","FOREGROUND"]
#CMD ["/usr/sbin/apache2ctl", "-DFOREGROUND"]
