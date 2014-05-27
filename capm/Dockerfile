FROM centos
MAINTAINER hatena@iti.co.jp


RUN yum update -y

## Add repos
RUN curl -O http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
RUN curl -O http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
RUN curl -O http://dl.iuscommunity.org/pub/ius/stable/Redhat/6/x86_64/ius-release-1.0-11.ius.el6.noarch.rpm
RUN curl -O http://rpms.famillecollet.com/RPM-GPG-KEY-remi; rpm --import RPM-GPG-KEY-remi
RUN rpm -Uvh remi-release-6*.rpm epel-release-6*.rpm ius-release-1.0-11.ius.el6.noarch.rpm

RUN yum -y --enablerepo=epel,remi install
RUN yum install -y openssh-server httpd mysql-server python-setuptools
RUN yum install -y yum install php54 php54-cli php54-common php54-gd php54-intl php54-mbstring php54-mcrypt php54-mysql php54-pdo php54-pecl-apc php54-xml php54-gd
RUN yum install ghostscript ghostscript-devel ghostscript-fonts ImageMagick ImageMagick-devel
RUN yum clean all
RUN easy_install supervisor
 
# sshd
ADD id_rsa.pub /root/id_rsa.pub
RUN mkdir -p /root/.ssh/
RUN cp /root/id_rsa.pub /root/.ssh/authorized_keys
RUN chmod 700 /root/.ssh
RUN chmod 600 /root/.ssh/authorized_keys
 
RUN sed -i -e '/^UsePAM\s\+yes/d' /etc/ssh/sshd_config
RUN /etc/init.d/sshd start && /etc/init.d/sshd stop
 
# mysqld
RUN touch /etc/sysconfig/network
RUN /etc/init.d/mysqld start && mysqladmin -u root password 'your_password' && /etc/init.d/mysqld stop
#RUN /etc/init.d/mysqld start && mysqladmin -u root password 'your_password' && (echo 'grant all privileges on *.* to root@"%" identified by "your_password" with grant option;' | mysql -u root -pyour_password) && /etc/init.d/mysqld stop

# docroot
RUN mkdir -p /srv/www
RUN sed -i 's#DocumentRoot "/var/www/html"#DocumentRoot "/srv/www"#' /etc/httpd/conf/httpd.conf

# supervisord
RUN mkdir -p /var/log/supervisor
ADD supervisord.conf /etc/supervisord.conf
 
EXPOSE 22 80 3306
 
CMD ["/usr/bin/supervisord"]

