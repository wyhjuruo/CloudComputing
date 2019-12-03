FROM centos:php
RUN yum install rsync -y
COPY wordpress-5.2.4-zh_CN.tar.gz /
RUN tar xzvf wordpress-5.2.4-zh_CN.tar.gz
RUN rsync -avP /wordpress/ /var/www/html/
#RUN mkdir  /var/www/html/wp-content/uploads
#RUN chown -R apache:apache /var/www/html/*
#暴露端口
EXPOSE  80                                                                                                                                   

