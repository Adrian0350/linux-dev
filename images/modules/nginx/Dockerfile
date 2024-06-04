FROM nginx:alpine

LABEL maintainer="jaime.ziga@gmail.com"

# Install openssl required for the self-signed SSL Certificates
RUN apk add openssl
RUN mkdir /etc/ssl/nginx
RUN mkdir /etc/ssl/nginx/private
RUN mkdir /etc/ssl/nginx/certs

# Generate self-signed SSL Certificate with the company information.
# Arguments are defined in docker-compose.yml which come from .env.
RUN openssl req \
	-x509 \
	-nodes \
	-days 365 \
	-newkey rsa:2048 \
	-subj "/C=${SSL_C}/ST=${SSL_ST}/L=${SSL_L}/O=${SSL_O}/CN=${SSL_CN}" \
	-keyout /etc/ssl/nginx/private/nginx-selfsigned.key \
	-out /etc/ssl/nginx/certs/nginx-selfsigned.crt

# While we are using OpenSSL, we should also create a strong Diffie-Hellman group,
# which is used in negotiating Perfect Forward Secrecy with clients.
RUN openssl dhparam -out /etc/ssl/nginx/certs/dhparam.pem 2048
