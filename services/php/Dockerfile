#指定基础镜像
ARG PHP_VERSION
FROM php:${PHP_VERSION}-fpm-alpine

ARG TZ
ARG REPOSITORIES

#替换仓库地址
RUN sed -i "s/dl-cdn.alpinelinux.org/${REPOSITORIES}/g" /etc/apk/repositories

COPY ./exts /tmp/exts
WORKDIR /tmp/exts

#安装依赖库
RUN apk --update add --no-cache --virtual .build-deps autoconf g++ libtool make curl-dev gettext-dev linux-headers

#安装PHP扩展
ARG PHP_PDO_MYSQL=false
RUN if [[ ${PHP_PDO_MYSQL} != false ]]; then \
    docker-php-ext-install pdo_mysql \
;fi

ARG PHP_PCNTL=false
RUN if [[ ${PHP_PCNTL} != false ]]; then \
    docker-php-ext-install pcntl \
;fi

ARG PHP_MYSQLI=false
RUN if [[ ${PHP_MYSQLI} != false ]]; then \
    docker-php-ext-install mysqli \
;fi

ARG PHP_EXIF=false
RUN if [[ ${PHP_EXIF} != false ]]; then \
    docker-php-ext-install exif \
;fi

ARG PHP_BCMATH=false
RUN if [[ ${PHP_BCMATH} != false ]]; then \
    docker-php-ext-install bcmath \
;fi

ARG PHP_CALENDAR=false
RUN if [[ ${PHP_CALENDAR} != false ]]; then \
    docker-php-ext-install calendar \
;fi

ARG PHP_ZEND_TEST=false
RUN if [[ ${PHP_ZEND_TEST} != false ]]; then \
    docker-php-ext-install zend_test \
;fi

ARG PHP_OPCACHE=false
RUN if [[ ${PHP_OPCACHE} != false ]]; then \
    docker-php-ext-install opcache \
;fi

ARG PHP_SOCKETS=false
RUN if [[ ${PHP_SOCKETS} != false ]]; then \
    docker-php-ext-install sockets \
;fi

ARG PHP_GETTEXT=false
RUN if [[ ${PHP_GETTEXT} != false ]]; then \
    docker-php-ext-install gettext \
;fi

ARG PHP_SHMOP=false
RUN if [[ ${PHP_SHMOP} != false ]]; then \
    docker-php-ext-install shmop \
;fi

ARG PHP_SYSVMSG=false
RUN if [[ ${PHP_SYSVMSG} != false ]]; then \
    docker-php-ext-install sysvmsg \
;fi

ARG PHP_SYSVSEM=false
RUN if [[ ${PHP_SYSVSEM} != false ]]; then \
    docker-php-ext-install sysvsem \
;fi

ARG PHP_SYSVSHM=false
RUN if [[ ${PHP_SYSVSHM} != false ]]; then \
    docker-php-ext-install sysvshm \
;fi

# ARG PHP_PDO_FIREBIRD=false
# RUN if [[ ${PHP_PDO_FIREBIRD} != false ]]; then \
#    # 该扩展已经没有维护了
#    docker-php-ext-install pdo_firebird \
# ;fi

ARG PHP_PDO_DBLIB=false
RUN if [[ ${PHP_PDO_DBLIB} != false ]]; then \
    # 错误 Cannot find FreeTDS in known installation directories
    apk --no-cache add freetds-dev && \
    docker-php-ext-install pdo_dblib \
;fi

ARG PHP_PDO_OCI=false
RUN if [[ ${PHP_PDO_OCI} != false ]]; then \
    echo "please install oci8" \
;fi

ARG PHP_PDO_ODBC=false
RUN if [[ ${PHP_PDO_ODBC} != false ]]; then \
    apk add --no-cache unixodbc-dev && \
    # 错误 configure: error: Unknown ODBC flavour yes The include and lib dirs are looked for under 'dir'
    docker-php-ext-configure pdo_odbc --with-pdo-odbc=unixODBC,/usr && \
    docker-php-ext-install pdo_odbc \
;fi

ARG PHP_PDO_PGSQL=false
RUN if [[ ${PHP_PDO_PGSQL} != false ]]; then \
    apk --no-cache add postgresql-dev && \
    docker-php-ext-install pdo_pgsql \
;fi

ARG PHP_PGSQL=false
RUN if [[ ${PHP_PGSQL} != false ]]; then \
    apk add --no-cache postgresql-dev && \
    docker-php-ext-install pgsql \
;fi

ARG PHP_OCI8=false
ENV LD_LIBRARY_PATH="/opt/oracle/instantclient_12_1"
ENV OCI_HOME="/opt/oracle/instantclient_12_1"
ENV OCI_LIB_DIR="/opt/oracle/instantclient_12_1"
ENV OCI_INCLUDE_DIR="/opt/oracle/instantclient_12_1/sdk/include"
ENV OCI_VERSION=12
RUN if [[ ${PHP_OCI8} != false ]]; then \
    apk --no-cache add libaio-dev freetds-dev unzip libnsl libc6-compat \
    && mkdir /opt/oracle \
    && unzip instantclient-basic-linux.x64-12.1.0.2.0.zip -d /opt/oracle \
    && unzip instantclient-sdk-linux.x64-12.1.0.2.0.zip -d /opt/oracle \
    && ln -s /opt/oracle/instantclient_12_1/libclntsh.so.12.1 /opt/oracle/instantclient_12_1/libclntsh.so \
    && ln -s /opt/oracle/instantclient_12_1/libclntshcore.so.12.1 /opt/oracle/instantclient_12_1/libclntshcore.so \
    && ln -s /opt/oracle/instantclient_12_1/libocci.so.12.1 /opt/oracle/instantclient_12_1/libocci.so \
    && ln -s /usr/lib/libnsl.so.2 /usr/lib/libnsl.so.1 \
    && ln -s /lib/libc.musl-x86_64.so.1 /lib/ld-linux-x86-64.so.2 \
    && rm -rf /opt/oracle/*.zip && \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        echo 'instantclient,/opt/oracle/instantclient_12_1/' | pecl install oci8-2.2.0.tgz; \
        # echo 'instantclient,/opt/oracle/instantclient_12_1/' | pecl install oci8-2.2.0; \
    else \
        echo 'instantclient,/opt/oracle/instantclient_12_1/' | pecl install oci8-1.4.10.tgz \
        # echo 'instantclient,/opt/oracle/instantclient_12_1/' | pecl install oci8 \
    ;fi && \
    docker-php-ext-configure pdo_oci --with-pdo-oci=instantclient,/opt/oracle/instantclient_12_1,12.1 \
    && docker-php-ext-install pdo_oci \
    && docker-php-ext-enable oci8 \
;fi

ARG PHP_ODBC=false
RUN if [[ ${PHP_ODBC} != false ]]; then \
    # https://github.com/docker-library/php/issues/103
    # https://github.com/docker-library/php/issues/732
    docker-php-source extract; \
	{ \
		echo '# https://github.com/docker-library/php/issues/103#issuecomment-271413933'; \
		echo 'AC_DEFUN([PHP_ALWAYS_SHARED],[])dnl'; \
		echo; \
		cat /usr/src/php/ext/odbc/config.m4; \
	} > temp.m4; \
	mv temp.m4 /usr/src/php/ext/odbc/config.m4; \
    apk add --no-cache unixodbc-dev && \
    docker-php-ext-configure odbc --with-unixODBC=shared,/usr && \
    docker-php-ext-install odbc \
;fi

ARG PHP_DBA=false
RUN if [[ ${PHP_DBA} != false ]]; then \
    docker-php-ext-install dba \
;fi

ARG PHP_GD=false
RUN if [[ ${PHP_GD} != false ]]; then \
    apk add --no-cache freetype freetype-dev libpng libpng-dev libjpeg-turbo libjpeg-turbo-dev && \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "4" ]]; then \
        docker-php-ext-configure gd --with-freetype --with-jpeg; \
    else \
        docker-php-ext-configure gd --with-gd --with-freetype-dir=/usr/include/ --with-png-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    ;fi && \
    docker-php-ext-install gd && apk del freetype-dev libpng-dev libjpeg-turbo-dev \
;fi

ARG PHP_INTL=false
RUN if [[ ${PHP_INTL} != false ]]; then \
    apk add --no-cache icu-dev && \
    docker-php-ext-install intl \
;fi

ARG PHP_BZ2=false
RUN if [[ ${PHP_BZ2} != false ]]; then \
    apk add --no-cache bzip2-dev && \
    docker-php-ext-install bz2 \
;fi

ARG PHP_SOAP=false
RUN if [[ ${PHP_SOAP} != false ]]; then \
    apk add --no-cache libxml2-dev && \
    docker-php-ext-install soap \
;fi

ARG PHP_XSL=false
RUN if [[ ${PHP_XSL} != false ]]; then \
    apk add --no-cache libxml2-dev libxslt-dev && \
    docker-php-ext-install xsl \
;fi

ARG PHP_XMLRPC=false
RUN if [[ ${PHP_XMLRPC} != false ]]; then \
    apk add --no-cache libxml2-dev libxslt-dev && \
    docker-php-ext-install xmlrpc \
;fi

# ARG PHP_WDDX=false
# RUN if [[ ${PHP_WDDX} != false ]]; then \
#    # wddx 在 http://pecl.php.net/ 已经没有维护了
#    apk add --no-cache libxml2-dev libxslt-dev && \
#	 docker-php-ext-install wddx \
# ;fi

ARG PHP_CURL=false
RUN if [[ ${PHP_CURL} != false ]]; then \
    echo "curl install" \
;fi

# ARG PHP_READLINE=false
# RUN if [[ ${PHP_READLINE} != false ]]; then \
#    apk add --no-cache readline-dev libedit-dev && \
#	 docker-php-ext-install readline \
# ;fi

ARG PHP_SNMP=false
RUN if [[ ${PHP_SNMP} != false ]]; then \
    apk add --no-cache net-snmp-dev && \
    docker-php-ext-install snmp \
;fi

ARG PHP_PSPELL=false
RUN if [[ ${PHP_PSPELL} != false ]]; then \
    apk add --no-cache aspell-dev aspell-en && \
    docker-php-ext-install pspell \
;fi

# ARG PHP_RECODE=false
# RUN if [[ ${PHP_RECODE} != false ]]; then \
#    # recode 在 http://pecl.php.net/ 已经没有维护了
#    apk add --no-cache recode-dev && \
#    docker-php-ext-install recode \
# ;fi

ARG PHP_TIDY=false
RUN if [[ ${PHP_TIDY} != false ]]; then \
    apk add --no-cache tidyhtml-dev && \
    ln -s /usr/include/tidybuffio.h /usr/include/buffio.h && \
    docker-php-ext-install tidy \
;fi

ARG PHP_GMP=false
RUN if [[ ${PHP_GMP} != false ]]; then \
    apk add --no-cache gmp-dev && \
    docker-php-ext-install gmp \
;fi

ARG PHP_IMAP=false
RUN if [[ ${PHP_IMAP} != false ]]; then \
    apk add --no-cache imap-dev && \
    docker-php-ext-configure imap --with-imap --with-imap-ssl && \
    docker-php-ext-install imap \
;fi

ARG PHP_LDAP=false
RUN if [[ ${PHP_LDAP} != false ]]; then \
    apk add --no-cache ldb-dev openldap-dev && \
    docker-php-ext-install ldap \
;fi

ARG PHP_IMAGICK=false
RUN if [[ ${PHP_IMAGICK} != false ]]; then \
    apk add --no-cache file-dev imagemagick-dev && \
    pecl install imagick-3.4.4.tgz && \
    # 网络环境可以的，可以直接在线安装
    # pecl install imagick-${PHP_IMAGICK} && \
    docker-php-ext-enable imagick \
;fi

ARG PHP_RAR=false
RUN if [[ ${PHP_RAR} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        echo "rar is no update"; \
    else \
        pecl install rar-4.0.0.tgz && \
        # pecl install rar && \
        docker-php-ext-enable rar \
    ;fi \
;fi

ARG PHP_AST=false
RUN if [[ ${PHP_AST} != false ]]; then \
    pecl install ast-1.0.6.tgz && \
    # pecl install ast && \
    docker-php-ext-enable ast \
;fi

ARG PHP_MSGPACK=false
RUN if [[ ${PHP_MSGPACK} != false ]]; then \
    pecl install msgpack-2.1.0.tgz && \
    # pecl install msgpack && \
    docker-php-ext-enable msgpack \
;fi

ARG PHP_IGBINARY=false
RUN if [[ ${PHP_IGBINARY} != false ]]; then \
    pecl install igbinary-3.1.2.tgz && \
    # pecl install igbinary && \
    docker-php-ext-enable igbinary \
;fi

ARG PHP_YAC=false
RUN if [[ ${PHP_YAC} != false ]]; then \
    pecl install yac-2.0.4.tgz && \
    # pecl install yac-${PHP_YAC} && \
    docker-php-ext-enable yac \
;fi

ARG PHP_YAR=false
RUN if [[ ${PHP_YAR} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        pecl install yar-2.1.1.tgz && \
        # pecl install yar && \
        docker-php-ext-enable yar \
    ;fi \
;fi

ARG PHP_YACONF=false
RUN if [[ ${PHP_YACONF} != false ]]; then \
    pecl install yaconf-1.1.0.tgz && \
    # pecl install yaconf && \
    docker-php-ext-enable yaconf \
;fi

ARG PHP_SEASLOG=false
RUN if [[ ${PHP_SEASLOG} != false ]]; then \
    pecl install SeasLog-2.1.0.tgz && \
    # pecl install seaslog && \
    docker-php-ext-enable seaslog \
;fi

ARG PHP_VARNISH=false
RUN if [[ ${PHP_VARNISH} != false ]]; then \
    apk add --no-cache varnish-dev && \
    pecl install varnish-1.2.4.tgz && \
    # pecl install varnish && \
    docker-php-ext-enable varnish \
;fi

ARG PHP_PDO_SQLSRV=false
RUN if [[ ${PHP_PDO_SQLSRV} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "1" ]]; then \
        apk add --no-cache unixodbc-dev && \
        pecl install pdo_sqlsrv-5.8.0.tgz && \
        # pecl install pdo_sqlsrv && \
        docker-php-ext-enable pdo_sqlsrv \
    ;fi \
;fi

ARG PHP_SQLSRV=false
RUN if [[ ${PHP_SQLSRV} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "1" ]]; then \
        apk add --no-cache unixodbc-dev && \
        pecl install sqlsrv-5.8.0.tgz && \
        # pecl install sqlsrv && \
        docker-php-ext-enable sqlsrv \
    ;fi \
;fi

ARG PHP_MCRYPT=false
RUN if [[ ${PHP_MCRYPT} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "2" ]]; then \
        echo "mcrypt remove"; \
    else \
        apk add --no-cache libmcrypt-dev && \
        docker-php-ext-install mcrypt \
    ;fi \
;fi

ARG PHP_MYSQL=false
RUN if [[ ${PHP_MYSQL} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        echo "mysql remove"; \
    else \
        docker-php-ext-install mysql \
    ;fi \
;fi

ARG PHP_SODIUM=false
RUN if [[ ${PHP_SODIUM} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "2" ]]; then \
        echo "sodium remove"; \
    else \
        apk add --no-cache libsodium-dev && \
        docker-php-ext-install sodium \
    ;fi \
;fi

ARG PHP_AMQP=false
RUN if [[ ${PHP_AMQP} != false ]]; then \
    apk add --no-cache rabbitmq-c-dev && \
    pecl install amqp-1.9.4.tgz && \
    # pecl install amqp-${PHP_AMQP} && \
    docker-php-ext-enable amqp \
;fi

ARG PHP_REDIS=false
RUN if [[ ${PHP_REDIS} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        pecl install redis-5.2.1.tgz; \
        # pecl install redis-${PHP_REDIS}; \
    else \
        pecl install redis-4.3.0.tgz \
        # pecl install redis-4.3.0 \
    ;fi && \
    docker-php-ext-enable redis \
;fi

ARG PHP_APCU=false
RUN if [[ ${PHP_APCU} != false ]]; then \
    pecl install apcu-5.1.18.tgz && \
    # pecl install apcu && \
    docker-php-ext-enable apcu \
;fi

ARG PHP_MEMCACHED=false
RUN if [[ ${PHP_MEMCACHED} != false ]]; then \
    apk add --no-cache libmemcached-dev zlib-dev && \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        pecl install memcached-3.1.5.tgz; \
        # pecl install memcached-${PHP_MEMCACHED}; \
    else \
        pecl install memcached-2.2.0.tgz \
        # pecl install memcached-2.2.0 \
    ;fi && \
    docker-php-ext-enable memcached \
;fi

ARG PHP_MEMCACHE=false
RUN if [[ ${PHP_MEMCACHE} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        pecl install memcache-4.0.5.2.tgz; \
        # pecl install memcache-${PHP_MEMCACHE}; \
    else \
        pecl install memcache-2.2.6.tgz \
        # pecl install memcache-2.2.6 \
    ;fi && \
    docker-php-ext-enable memcache \
;fi

ARG PHP_XDEBUG=false
RUN if [[ ${PHP_XDEBUG} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "4" ]]; then \
            pecl install xdebug-2.9.3.tgz; \
            # pecl install xdebug-${PHP_XDEBUG}; \
        else \
            pecl install xdebug-2.6.1.tgz \
            # pecl install xdebug-2.6.1 \
        ;fi \
    else \
        pecl install xdebug-2.5.5.tgz \
        # pecl install xdebug-2.5.5 \
    ;fi && \
    docker-php-ext-enable xdebug \
;fi

ARG PHP_EVENT=false
RUN if [[ ${PHP_EVENT} != false ]]; then \
    apk add --no-cache libevent-dev && \
    export is_sockets_installed=$(php -r "echo extension_loaded('sockets');") && \
    if [[ "${is_sockets_installed}" = "" ]]; then \
        docker-php-ext-install sockets \
    ;fi && \
    pecl install event-2.5.4.tgz && \
    # pecl install event-${PHP_EVENT} && \
    docker-php-ext-enable event --ini-name event.ini \
;fi

ARG PHP_MONGODB=false
RUN if [[ ${PHP_MONGODB} != false ]]; then \
    pecl install mongodb-1.7.4.tgz && \
    # pecl install mongodb-1.5.5 && \
    docker-php-ext-enable mongodb \
;fi

ARG PHP_YAF=false
RUN if [[ ${PHP_YAF} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        pecl install yaf-3.1.3.tgz; \
        # pecl install yaf-${PHP_YAF}; \
    else \
        pecl install yaf-2.3.5.tgz \
        # pecl install yaf-2.3.5 \
    ;fi && \
    docker-php-ext-enable yaf \
;fi

ARG PHP_SWOOLE=false
RUN if [[ ${PHP_SWOOLE} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        pecl install swoole-4.4.16.tgz; \
        # pecl install swoole-${PHP_SWOOLE}; \
    else \
        pecl install swoole-2.0.11.tgz \
        # pecl install swoole-2.0.11 \
    ;fi && \
    docker-php-ext-enable swoole \
;fi

ARG PHP_ZIP=false
RUN if [[ ${PHP_ZIP} != false ]]; then \
    apk add --no-cache libzip-dev && \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "3" ]]; then \
        docker-php-ext-configure zip; \
    else \
        docker-php-ext-configure zip --with-libzip=/usr/include \
    ;fi && \
    docker-php-ext-install zip \
;fi

ARG PHP_XHPROF=false
RUN if [[ ${PHP_XHPROF} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        pecl install xhprof-2.1.3.tgz && \
        # pecl install xhprof-${PHP_XHPROF} && \
        docker-php-ext-enable xhprof \
    ;fi \
;fi

ARG PHP_XLSWRITER=false
RUN if [[ ${PHP_XLSWRITER} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        pecl install xlswriter-1.3.4.tgz && \
        # pecl install xlswriter && \
        docker-php-ext-enable xlswriter \
    ;fi \
;fi

ARG PHP_TAINT=false
RUN if [[ ${PHP_TAINT} != false ]]; then \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        pecl install taint-2.0.6.tgz; \
        # pecl install taint-${PHP_TAINT}; \
    else \
        pecl install taint-1.2.2.tgz \
        # pecl install taint-1.2.2 \
    ;fi && \
    docker-php-ext-enable taint \
;fi

ARG PHP_YAML=false
RUN if [[ ${PHP_YAML} != false ]]; then \
    apk add --no-cache yaml yaml-dev && \
    if [[ "$(php -r "echo PHP_MAJOR_VERSION;")" -gt "7" || "$(php -r "echo PHP_MAJOR_VERSION;")" -eq "7" && "$(php -r "echo PHP_MINOR_VERSION;")" -ge "0" ]]; then \
        pecl install yaml-2.0.4.tgz; \
        # pecl install yaml-${PHP_YAML}; \
    else \
        pecl install yaml-1.3.1.tgz \
        # pecl install yaml-1.3.1 \
    ;fi && \
    docker-php-ext-enable yaml \
;fi

#清除安装文件
RUN apk del .build-deps && docker-php-source delete && rm -rf /tmp/exts && rm -rf /tmp/pear

#设置时区
RUN apk --no-cache add tzdata \
    && cp "/usr/share/zoneinfo/$TZ" /etc/localtime \
    && echo "$TZ" > /etc/timezone

#Fix: https://github.com/docker-library/php/issues/240
RUN apk add gnu-libiconv libstdc++ --no-cache --repository http://${REPOSITORIES}/alpine/edge/community/ --allow-untrusted
ENV LD_PRELOAD /usr/lib/preloadable_libiconv.so php

#安装composer
RUN curl -o /usr/bin/composer https://mirrors.aliyun.com/composer/composer.phar \
    && chmod +x /usr/bin/composer

#修改用户
RUN apk --no-cache add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data

#设置工作目录
WORKDIR /wwwroot