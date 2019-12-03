
FROM centos:7

LABEL project="Dockerfile for Apache Web"

RUN yum -y install httpd

EXPOSE 80

VOLUME /var/www/html

ENTRYPOINT [ "/usr/sbin/httpd" ]
CMD ["-D", "FOREGROUND"]

