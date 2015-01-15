FROM ubuntu:14.04
MAINTAINER Greg Moser <greg@gregmoser.com>

#Install base packages
RUN cd ~
RUN apt-get -y update
RUN apt-get install -y openssh-server openjdk-7-jre-headless tomcat7 tomcat7-admin apache2 libapache2-mod-jk supervisor nano wget
RUN apt-get purge -y openjdk-6-jre-headless icedtea-6-jre-cacao openjdk-6-jre-lib icedtea-6-jre-jamvm

# Setup SSH
RUN mkdir /var/run/sshd
RUN echo 'root:screencast' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

# config to enable .htaccess
ADD _deploy/apache_default /etc/apache2/sites-available/000-default.conf
RUN a2enmod rewrite

ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

# install railo
RUN wget http://www.getrailo.org/railo/remote/download42/4.2.1.008/tomcat/linux/railo-4.2.1.008-pl0-linux-x64-installer.run

RUN chmod -R 744 railo-4.2.1.008-pl0-linux-x64-installer.run
RUN ./railo-4.2.1.008-pl0-linux-x64-installer.run --mode unattended --railopass "123456"

# Remove railo setup
RUN rm -rf railo-4.2.1.008-pl2-linux-x64-installer.run

# EXPOSE <port>
EXPOSE 22 80 8888

# Supervisord configuration
ADD ./supervisord.conf /etc/supervisor/conf.d/supervisord.conf
CMD ["/usr/bin/supervisord"]