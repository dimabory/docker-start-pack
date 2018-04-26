FROM debian:jessie

RUN apt-get -qq update && apt-get -qq install -y \
    nginx

ADD ./config/nginx.conf /etc/nginx/
ADD ./config/default.conf /etc/nginx/sites-available/
RUN unlink /etc/nginx/sites-available/default

RUN usermod -u 1000 www-data

CMD ["nginx"]

EXPOSE 80
EXPOSE 443
