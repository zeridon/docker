FROM ubuntu:trusty

RUN sudo locale-gen en_US.UTF-8 && export LANG=en_US.UTF-8
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install software-properties-common -yq
RUN apt-get install -y --force-yes \
        php-apc php-gettext php-pear php5 php5-apcu php5-common php5-curl php5-fpm php5-gd php5-imap php5-json php5-mcrypt php5-mysql php5-xmlrpc 

COPY local.ini /etc/php5/mods-available/local.ini
COPY www.conf /etc/php5/fpm/pool.d/www.conf
COPY php-fpm.conf /etc/php5/fpm/php-fpm.conf

RUN php5enmod -s ALL local && php5-fpm -i |grep disable_functions

ENV PHP_FPM_USER=www-data

RUN apt-get install supervisor -y

COPY etc/supervisor/conf.d/php-fpm.conf /etc/supervisor/conf.d/php-fpm.conf
COPY etc/supervisor/conf.d/nginx.conf /etc/supervisor/conf.d/nginx.conf
COPY etc/supervisor/conf.d/monit.conf /etc/supervisor/conf.d/monit.conf
COPY etc/supervisor/conf.d/mysql-proxy.conf /etc/supervisor/conf.d/mysql-proxy.conf

RUN  LC_ALL=en_US.UTF-8 add-apt-repository ppa:chris-lea/nginx-devel -y && apt-get update && apt-get install nginx-full -y

COPY nginx.conf /etc/nginx/nginx.conf

######### nss start
RUN apt-get install -y libnss-extrausers
COPY etc/nsswitch.conf /etc/nsswitch.conf
######### nss end

####### monit start
RUN apt-get install monit -y
COPY etc/monit/monitrc /etc/monit/monitrc
COPY etc/monit/conf.d/notify.cfg /etc/monit/conf.d/notify.cfg
####### monit end

######### mysql proxy start
RUN apt-get install mysql-proxy -y
COPY etc/mysql/mysql-proxy.cnf /etc/mysql/mysql-proxy.cnf
RUN chmod 600 /etc/mysql/mysql-proxy.cnf
######### mysql proxy end

RUN mkdir -p /var/www

CMD supervisord -c /etc/supervisor/supervisord.conf -n


