FROM php:7.4-fpm

ARG TIMEZONE
ARG USER
ARG USER_NAME
ARG USER_EMAIL
ARG USER_ID
ARG XDEBUG_HOST
ARG XDEBUG_PORT
ARG XDEBUG_DISCOVER_CLIENT_HOST

# Install APT basic packages
RUN apt update && apt install -y git-all git-flow unzip vim curl gnupg bash-completion libyaml-dev iputils-ping

# Install APT packages nodejs & yarn
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash - && \
    apt-get update && \
    apt-get install -y --no-install-recommends nodejs && \
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
    apt-get update && \
    apt-get install -y --no-install-recommends yarn && \
    npm install -g npm

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone
RUN printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini
RUN "date"

# Type docker-php-ext-install to see available extensions
RUN docker-php-ext-install pdo_mysql bcmath
RUN pecl channel-update pecl.php.net
RUN pecl install apcu
RUN docker-php-ext-enable apcu
RUN pecl install yaml
RUN docker-php-ext-enable yaml
RUN pecl install redis
RUN docker-php-ext-enable redis

# install xdebug
RUN pecl install xdebug
RUN docker-php-ext-enable xdebug
RUN echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "display_startup_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "display_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.mode=coverage,debug,develop" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.start_with_request=yes" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.client_host=${XDEBUG_HOST}" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.client_port=${XDEBUG_PORT}" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.discover_client_host=${XDEBUG_DISCOVER_CLIENT_HOST}" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer

# Sonar Scanner
RUN npm install -g sonarqube-scanner

# Create user
RUN groupadd ${USER}
RUN useradd -g ${USER} -u ${USER_ID} -d /home/${USER} -m -s /bin/bash ${USER}
COPY docker/bash/ssh/id_rsa* /home/${USER}/.ssh/
COPY docker/bash/.gitconfig /home/${USER}/
COPY docker/bash/.bashrc /home/${USER}/.bashrc
COPY docker/bash/.bash_aliases /home/${USER}/.bash_aliases

RUN \
    chown -R ${USER}:${USER} /home/${USER} && \
    sed -i "s/EMAIL/${USER_EMAIL}/g" /home/${USER}/.gitconfig && \
    sed -i "s/NAME/${USER_NAME}/g"  /home/${USER}/.gitconfig && \
    chmod 600 /home/${USER}/.ssh/id_rsa && \
    chmod 600 /home/${USER}/.ssh/id_rsa.pub && \
    mkdir /var/www/app && \
    chown -R ${USER}:${USER} /var/www/app && \
    mkdir -p /var/log/nginx/ && \
    chmod -R 0777 /var/log/nginx

WORKDIR /var/www/app
