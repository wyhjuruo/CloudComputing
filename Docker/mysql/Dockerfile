FROM centos:php
#
RUN yum install mariadb-server mariadb -y

#
WORKDIR /root
#
ADD business.sql /root/business.sql
ADD server.cnf /etc/my.cnf.d/server.cnf
ADD setup.sh /root/setup.sh

RUN yum clean all
RUN chmod +x /root/setup.sh
EXPOSE 3306

CMD ["/root/setup.sh"]
