FROM php:7.2.11-fpm-stretch

ENV DEBIAN_FRONTEND noninteractive

# Install what we need
RUN apt-get update && apt-get upgrade -y && apt-get install -y \
	git \
	libav-tools \
	nginx \
	unzip \
	supervisor

# Install Composer
COPY docker-install-composer /usr/local/bin
RUN docker-install-composer

# Install pdo_mysql (script from the php image)
RUN docker-php-ext-install pdo_mysql


# Install Sonerezh
RUN git clone --branch 1.2.4 --depth 1 https://github.com/Sonerezh/sonerezh.git /usr/share/nginx/sonerezh && \
	chown -R www-data: /usr/share/nginx/sonerezh && \
	chmod 775 -R /usr/share/nginx/sonerezh

RUN cd /usr/share/nginx/sonerezh && composer install
# Prepare volume
RUN mkdir /music && \
	ln -s /usr/share/nginx/sonerezh/app/webroot/img/thumbnails /thumbnails && \
	chown www-data: /music

VOLUME /music
VOLUME /thumbnails

# Copy image configuration
RUN rm -f /etc/nginx/sites-enabled/default
COPY database.php /usr/share/nginx/sonerezh/app/Config/database.php
COPY default /etc/nginx/sites-enabled/default
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY docker-entrypoint.sh /entrypoint.sh

EXPOSE 80
ENTRYPOINT ["/entrypoint.sh"]
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]
