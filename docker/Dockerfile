FROM nanoninja/php-fpm:8.1

ARG TIMEZONE
ARG USER
ARG USER_NAME
ARG USER_EMAIL
ARG USER_ID
ARG XDEBUG_CLIENT_PORT

# Install APT sources nodejs & yarn
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

# Install APT packages
RUN apt update && apt install -y git unzip vim curl gnupg bash-completion libyaml-dev iputils-ping nodejs yarn

# Sonar Scanner
RUN npm i -g sonarqube-scanner

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone
RUN printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini
RUN "date"

# install Xdebug
RUN echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "display_startup_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "display_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.mode=debug,coverage" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.client_port = ${XDEBUG_CLIENT_PORT}" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.discover_client_host = 1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.start_with_request = yes" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.log_level = 0" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer

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
