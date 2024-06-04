FROM php:7.4.33-fpm

LABEL maintainer="jaime.ziga@gmail.com"

SHELL ["/bin/bash", "-c"] 
RUN chsh -s /bin/bash root

# Install libs that will be required when installing repository dependencies.
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

RUN apt update -y

RUN apt install -y libmcrypt-dev \
                        libxml2 \
                        libpng-dev \
                        g++ \
                        libicu-dev \
                        libmcrypt4 \
                        zlib1g-dev \
                        git \
                        inetutils-ping \
                        zip \
                        unzip \
                        nano \
                        libzip-dev \
                        libxslt1-dev \
                        libyaml-dev \
                        gettext \
                        openssl \
                        libxml2-dev \
                        vim \
                        redis \
                        pv \
                        gpg \
                        bash-completion

RUN pecl install igbinary
RUN pecl install redis
RUN pecl install mcrypt
RUN pecl install yaml

RUN docker-php-ext-enable mcrypt
RUN docker-php-ext-enable redis
RUN docker-php-ext-enable yaml

RUN docker-php-ext-install pdo_mysql
RUN docker-php-ext-install intl
RUN docker-php-ext-install json
RUN docker-php-ext-install gd
RUN docker-php-ext-install iconv
RUN docker-php-ext-install opcache
RUN docker-php-ext-install pcntl
RUN docker-php-ext-install zip
RUN docker-php-ext-install xsl
RUN docker-php-ext-install soap

# Install libbson for mongodb extension
RUN curl -L -O https://github.com/mongodb/libbson/releases/download/1.6.3/libbson-1.6.3.tar.gz && \
    tar -xvf libbson-1.6.3.tar.gz && cd libbson-1.6.3 && \
    ./configure --prefix=/usr --libdir=/usr/lib && make install && cd && \
    rm libbson-1.6.3.tar.gz && rm -rf libbson-1.6.3

# Now install mongodb extension
RUN pecl install mongodb

RUN chmod +x /usr/local/bin/install-php-extensions && sync && \
    install-php-extensions mbstring \
    gearman

# Install composer globally
RUN curl -sSL https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer

# Install NVM - Node - Grunt and PM2
ENV NVM_DIR /root/.nvm
ENV NODE_VERSION v16.20.2
RUN mkdir -p /root/.nvm
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
RUN /bin/bash -c "source $NVM_DIR/nvm.sh && nvm install $NODE_VERSION && nvm use --delete-prefix $NODE_VERSION"
ENV NODE_PATH $NVM_DIR/versions/node/$NODE_VERSION/bin
ENV PATH $NODE_PATH:$PATH

RUN npm install -g grunt-cli pm2
