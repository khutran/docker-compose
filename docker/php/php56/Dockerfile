FROM centos:latest

MAINTAINER "Dylan Lindgren" <dylan.lindgren@gmail.com>

ENV port 9056
# Install required repos, update, and then install PHP-FPM
RUN cd ~ && \
    rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

RUN    yum -y update
RUN    yum install -y php56w-cli \
    php56w-fpm \
    php56w-devel \
    php56w-mysqlnd \
    php56w-xml \
    php56w-gd \
    php56w-mcrypt \
    php56w-ldap \
    php56w-xmlrpc \
    php56w-mbstring \
    php56w-pdo

# Configure and secure PHP
RUN sed -i "s/;date.timezone =.*/date.timezone = UTC/" /etc/php.ini && \
    sed -i -e "s/;daemonize\s*=\s*yes/daemonize = no/g" /etc/php-fpm.conf && \
    sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php.ini && \
    sed -i "s/short_open_tag = Off/short_open_tag = On/" /etc/php.ini && \
    sed -i '/^listen = /clisten = 0.0.0.0:${port}' /etc/php-fpm.d/www.conf && \
    sed -i '/^listen.allowed_clients/c;listen.allowed_clients =' /etc/php-fpm.d/www.conf && \
    sed -i '/^;catch_workers_output/ccatch_workers_output = yes' /etc/php-fpm.d/www.conf && \
    sed -i "s/php_admin_flag\[log_errors\] = .*/;php_admin_flag[log_errors] =/" /etc/php-fpm.d/www.conf && \
    sed -i "s/php_admin_value\[error_log\] =.*/;php_admin_value[error_log] =/" /etc/php-fpm.d/www.conf && \
    sed -i "s/php_admin_value\[error_log\] =.*/;php_admin_value[error_log] =/" /etc/php-fpm.d/www.conf && \
    echo "php_admin_value[display_errors] = 'stderr'" >> /etc/php-fpm.d/www.conf

# DATA VOLUMES
#RUN mkdir -p /data/nginx/www
#VOLUME ["/data/nginx/www"]

# PORTS
# Port 9000 is how Nginx will communicate with PHP-FPM.
EXPOSE ${port}

# Run PHP-FPM on container start.
ENTRYPOINT ["/usr/sbin/php-fpm", "-F"]
