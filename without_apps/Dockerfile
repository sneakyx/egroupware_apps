FROM php:5.6-apache
MAINTAINER André Scholz <info@rothaarsystems.de>

ENV DEBIAN_FRONTEND noninteractive
ARG egr_timezone=Europe/Berlin
RUN apt-get update \
        && apt-get install -y wget bzip2 zlib1g-dev re2c libmcrypt-dev pwgen \
        && wget -P /var/www https://github.com/EGroupware/egroupware/releases/download/16.1.20160810/egroupware-epl-16.1.20160810.tar.bz2 \
        && mv /var/www/egroupware*.tar.bz2 /var/www/egroupware.tar.bz2 \
        && tar -xjf /var/www/egroupware.tar.bz2 -C /var/www/html \
        && rm /var/www/egroupware.tar.bz2
# start manual installation

RUN docker-php-ext-install mysqli \
        && docker-php-ext-install pdo_mysql \
        && docker-php-ext-install zip \
        && docker-php-ext-install mcrypt \
        && docker-php-ext-install mbstring \
        && apt-get -y install libtidy-dev libjpeg62-turbo-dev libpng12-dev libldap2-dev \
        && docker-php-ext-install tidy \
        && docker-php-ext-install bcmath \
        && docker-php-ext-configure gd --with-jpeg-dir=/usr/lib \
        && docker-php-ext-install gd \
        && docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/  \
        && docker-php-ext-install ldap
# edit php.ini

RUN touch /usr/local/etc/php/conf.d/uploads.ini \
    && echo date.timezone = $egr_timezone  >> /usr/local/etc/php/conf.d/uploads.ini \
    && echo session.save_path = /var/tmp  >> /usr/local/etc/php/conf.d/uploads.ini


COPY docker-entrypoint.sh /entrypoint.sh 
COPY assets/apache.conf /etc/apache2/apache2.conf
# there are two updated files
# because manual installation of egroupware leaves some infos blank
COPY assets/class*.* /var/www/html/egroupware/setup/inc/

RUN chmod +x /entrypoint.sh \
	&& chmod 644 /var/www/html/egroupware/setup/inc/*.* 

EXPOSE 80 443

ENTRYPOINT ["/entrypoint.sh"]
CMD ["app:start"]