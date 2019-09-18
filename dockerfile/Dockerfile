FROM amd64/php:7.2-apache

ENV GLPI_VERSION=9.2.1
ENV GLPI_URL=https://github.com/glpi-project/glpi/releases/download/$GLPI_VERSION/glpi-$GLPI_VERSION.tgz
ENV TERM=xterm

RUN mkdir -p /usr/src/php/ext/

RUN apt-get update --no-install-recommends -yqq && \
	apt-get install --no-install-recommends -yqq \
	zlib1g \
	cron \
	bzip2 \
	wget \
	nano


RUN apt-get install -y libz-dev && \
    curl -o zip.tgz -SL http://pecl.php.net/get/zip-1.15.1.tgz && \
        tar -xf zip.tgz -C /usr/src/php/ext/ && \
        rm zip.tgz && \
       	mv /usr/src/php/ext/zip-1.15.1 /usr/src/php/ext/zip && \
		docker-php-ext-install zip

RUN apt-get install --no-install-recommends -y libldap2-dev && \
    docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ && \
	docker-php-ext-install ldap

RUN a2enmod rewrite expires

RUN apt-get install --no-install-recommends -yqq libssl-dev libc-client2007e-dev libkrb5-dev && \
    docker-php-ext-configure imap --with-imap-ssl --with-kerberos && \
    docker-php-ext-install imap

RUN apt-get install --no-install-recommends -yqq libbz2-dev && \
	docker-php-ext-install bz2

RUN apt-get install --no-install-recommends -yqq  re2c libmcrypt-dev libmcrypt4 libmcrypt-dev && \
    curl -o mcrypt.tgz -SL http://pecl.php.net/get/mcrypt-1.0.1.tgz && \
        tar -xf mcrypt.tgz -C /usr/src/php/ext/ && \
        rm mcrypt.tgz && \
        mv /usr/src/php/ext/mcrypt-1.0.1 /usr/src/php/ext/mcrypt && \
		docker-php-ext-install mcrypt

RUN apt-get --no-install-recommends -yqq  install zlib1g-dev && \
    docker-php-ext-install zip && \
    apt-get purge --auto-remove -y zlib1g-dev

RUN docker-php-ext-install mysqli

RUN docker-php-ext-install pdo_mysql

RUN apt-get --no-install-recommends -yqq  install libxml2-dev && \
	docker-php-ext-install soap

RUN apt-get --no-install-recommends -yqq  install libxslt-dev && \
	docker-php-ext-install xmlrpc xsl

RUN curl -o apcu.tgz -SL http://pecl.php.net/get/apcu-5.1.9.tgz && \
	tar -xf apcu.tgz -C /usr/src/php/ext/ && \
	rm apcu.tgz && \
	mv /usr/src/php/ext/apcu-5.1.9 /usr/src/php/ext/apcu && \
	docker-php-ext-install apcu

RUN apt-get install --no-install-recommends --fix-missing -yqq libicu-dev libfreetype6-dev libpng-dev libpng16-16 libjpeg-dev libjpeg62-turbo-dev libzip-dev && \
    docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/  && \
	docker-php-ext-install gd



RUN docker-php-ext-install opcache && \
	{ \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=2'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini


RUN cd /var/www/html && \
	wget ${GLPI_URL} && \
	tar --strip-components=1 -xvf glpi-${GLPI_VERSION}.tgz && \
	rm -f glpi-${GLPI_VERSION}.tgz


RUN chown -R www-data:www-data /var/www/html/*


COPY docker-entrypoint.sh /docker-entrypoint.sh
RUN  chmod +x /docker-entrypoint.sh


RUN  mkdir -p /var/www/html/files/_cache && \
	 chmod -R 775 /var/www/html/files/_cache && \
	 chown www-data:www-data /var/www/html/files/_cache

WORKDIR /var/www/html

EXPOSE 80

ENTRYPOINT [ "/docker-entrypoint.sh" ]
CMD ["apache2-foreground"]