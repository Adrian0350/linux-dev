FROM rockylinux:8.9

LABEL mainteiner="jaime.ziga@gmail.com"

# User and Enviroments
RUN groupadd pr0xy && useradd -g pr0xy pr0xy

# Installing utilities
RUN yum install -y \
	unzip \
	yum-utils \
	bind-utils \
	curl \
	git \
	gettext \
	which \
	nano \
	vim

# Installing Node, NPM & packages
RUN yum install -y nodejs npm
RUN npm install -g pm2 grunt-cli n
RUN n 16.20.2
RUN npm install -g npm@8.19.4

 # Install NGINX
RUN yum install -y nginx 

# Clearing
RUN rm -rf /var/cache/yum/*

# Setting development environment
ENV ENV_NODE development

RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == \
systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;

VOLUME [ "/sys/fs/cgroup" ]

CMD ["/usr/sbin/init"]
