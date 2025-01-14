FROM centos:latest
MAINTAINER https://github.com/CentOS/sig-cloud-instance-images

LABEL Vendor="CentOS" \
        License=GPLv2 \
        Version=2.4.6-40

ENV nginxversion="1.12.2-1" \
        os="centos" \
        osversion="7" \
        elversion="7_4" \
        port_apache=6669

RUN     yum install -y epel-release && \
        rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm && \
        yum update -y && \
        yum install -y php71w-cli \
        php71w \
        php71w-fpm \
        php71w-mysqlnd \
        php71w-xml \
        php71w-gd \
        php71w-mcrypt \
        php71w-xmlrpc \
        php71w-json \
        php71w-mbstring \
        php71w-pdo \
        php71w-common \
        php71w-devel

RUN yum install -y openssl sed &&\
        yum -y install wget && \
        yum -y autoremove &&\
        yum clean all &&\
        wget http://nginx.org/packages/$os/$osversion/x86_64/RPMS/nginx-$nginxversion.el$elversion.ngx.x86_64.rpm &&\
        rpm -iv nginx-$nginxversion.el$elversion.ngx.x86_64.rpm &&\
        sed -i '1i\
        daemon off;\
        ' /etc/nginx/nginx.conf && \
        rm -rf nginx*

RUN yum -y --setopt=tsflags=nodocs update && \
        yum -y install epel-release && \
        yum -y --setopt=tsflags=nodocs install httpd && \
        yum clean all && \ 
        yum -y install yum-changelog && \
        yum changelog httpd && \
        cd /etc/yum.repos.d && \
        wget https://repo.codeit.guru/codeit.el`rpm -q --qf "%{VERSION}" $(rpm -q --whatprovides redhat-release)`.repo && \
        yum -y install httpd && \
        mkdir /etc/httpd/conf.d/web
#sed -i s/80/${port_apache}/g /etc/httpd/conf/httpd.conf && \
#sed -i 's/DirectoryIndex index.html/DirectoryIndex index.php index.html/g' /etc/httpd/conf/httpd.conf && \
#echo "IncludeOptional conf.d/web/*.conf" >> /etc/httpd/conf/httpd.conf

# install supervisord
RUN yum -y install python-setuptools && \
        easy_install supervisor && \
        echo_supervisord_conf > /etc/supervisord.conf && \
        mkdir -p /var/log/supervisor && \
        mkdir -p /var/log/supervisor && \
        sed -i -e "s/^nodaemon=false/nodaemon=true/" /etc/supervisord.conf && \
        mkdir /etc/supervisord.d && \
        echo [include] >> /etc/supervisord.conf && \
        echo 'files = /etc/supervisord.d/*.ini' >> /etc/supervisord.conf

#install nodejs
RUN yum -y install git && \
        curl --silent --location https://rpm.nodesource.com/setup_8.x | bash - && \
        curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | tee /etc/yum.repos.d/yarn.repo && \
        yum -y install nodejs && \
        yum -y install yarn && \
        yum -y install gcc-c++ make && \
        yum -y install libgcrypt-devel && \
        yum clean all

#install forever
RUN npm install forever -g

#config nginx
RUN rm -rf /etc/nginx/nginx.conf 
ADD nginx.conf /etc/nginx/
#config apache 
RUN rm -rf /etc/httpd/conf/httpd.conf
ADD httpd.conf /etc/httpd/conf/

EXPOSE 80 443

# Simple startup script to avoid some issues observed with container restart
ADD run/mysql /usr/sbin/
ADD run/mysqldump /usr/sbin/

ADD run-httpd.sh /run-httpd.sh
RUN chmod -v +x /run-httpd.sh

CMD ["/run-httpd.sh"]
