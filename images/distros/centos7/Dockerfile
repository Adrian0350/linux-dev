FROM centos:centos7

LABEL maintainer="jaime.ziga@gmail.com"

# User and Enviroments
RUN groupadd pr0xy && useradd -g pr0xy pr0xy
ENV MYSQL_PACKAGE_URL https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
ENV MYSQL_RPM_GPG_KEY https://repo.mysql.com/RPM-GPG-KEY-mysql-2022

# Installing utilities
RUN yum install -y \
	unzip \
	epel-release \
	yum-utils \
	bind-utils \
	git \
	wkhtmltopdf \
	gearmand \
	gettext \
	which \
	nano \
	vim

# Install SSH, SCP, etc.
RUN yum install -y openssh-server openssh-clients

# Installing MySQL server
VOLUME /var/lib/mysql

RUN yum clean all
RUN rpmkeys --import $MYSQL_RPM_GPG_KEY
RUN yum install -y $MYSQL_PACKAGE_URL
RUN yum install -y mysql

# Installing Node, NPM & packages
RUN yum install -y nodejs npm
RUN npm install -g pm2 grunt-cli n
RUN n 16.20.2
RUN npm install -g npm@8.19.4

# Installing JSCS and JSHint
RUN npm install -g jscs
RUN npm install -g jshint

# Installing PHP 7.4
RUN yum install -y \
	https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm \
	http://rpms.remirepo.net/enterprise/remi-release-7.rpm

RUN	yum-config-manager --enable remi-php74
RUN yum install -y php \
	php-pecl-redis \
	php-fpm \
	php-gd \
	php-pecl-gearman \
	php-mbstring \
	php-pecl-yaml \
	php-mcrypt \
	php-mysql \
	php-pdo \
	php-opcache \
	php-pecl-xdebug \
	php-xml \
	php-zip

RUN sed -ie "s/;date.timezone =/date.timezone = America\/Mexico_City/" /etc/php.ini && \
	sed -ie "s/upload_max_filesize = 2M/upload_max_filesize = 5M/" /etc/php.ini

RUN sed -ie "s/group = apache/group = pr0xy/" /etc/php-fpm.d/www.conf && \
	sed -ie "s/user = apache/user = pr0xy/" /etc/php-fpm.d/www.conf

# Installing Composer
ENV PATH "/composer/vendor/bin:$PATH"

ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /composer
ENV COMPOSER_VERSION 2.1.1

RUN curl -s -f -L -o /tmp/installer.php https://raw.githubusercontent.com/composer/getcomposer.org/da290238de6d63faace0343efbdd5aa9354332c5/web/installer \
 && php -r " \
    \$signature = '669656bab3166a7aff8a7506b8cb2d1c292f042046c5a994c43155c0be6190fa0355160742ab2e1c88d40d5be660b410'; \
    \$hash = hash('SHA384', file_get_contents('/tmp/installer.php')); \
    if (!hash_equals(\$signature, \$hash)) { \
        unlink('/tmp/installer.php'); \
        echo 'Integrity check failed, installer is either corrupt or worse.' . PHP_EOL; \
        exit(1); \
    }" \
 && php /tmp/installer.php --no-ansi --install-dir=/usr/bin --filename=composer --version=${COMPOSER_VERSION} \
 && rm /tmp/installer.php \
 && composer --ansi --version --no-interaction

 # Install NGINX
RUN yum install -y nginx 

# Clearing
RUN rm -rf /var/cache/yum/*

# Setting development environment
ENV ENV_NODE development

################################# Uncomment the following to test OS in Docker #################################
RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == \
	systemd-tmpfiles-setup.service ] || rm -f $i; done); \
	rm -f /lib/systemd/system/multi-user.target.wants/*;\
	rm -f /etc/systemd/system/*.wants/*;\
	rm -f /lib/systemd/system/local-fs.target.wants/*; \
	rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
	rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
	rm -f /lib/systemd/system/basic.target.wants/*;\
	rm -f /lib/systemd/system/anaconda.target.wants/*;

RUN systemctl php-fpm start
RUN systemctl mysqld start

VOLUME [ “/sys/fs/cgroup” ]

CMD ["/usr/sbin/init"]
