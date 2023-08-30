---
title: 升级站点php版本到7.2.1
date: 2018-02-01 15:30:49
tags: php
---

php7已经问世两年多了，都说性能提升了不少，可开发一直在使用php5.6，最近考虑把自己站点的php版本升级一下，目前站点的php版本是5.6.31

```
~$ php -v
PHP 5.6.31 (cli) (built: Sep 14 2017 18:12:46)
Copyright (c) 1997-2016 The PHP Group
Zend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies
    with Zend OPcache v7.0.6-dev, Copyright (c) 1999-2016, by Zend Technologies
```

[php download](http://php.net/downloads.php)目前官网的稳定版本是7.2.1，下面记录下升级过程

<!-- more -->

目前版本5.6.31是通过yum安装的，php7我想通过源码安装

1. 首先先在本地下载源码包，通过rsync命令上传到服务器某个目录
2. 按照官方文档的安装步骤[php install](http://php.net/manual/zh/install.unix.nginx.php)解压
   
   ```
   tar zxf php-x.x.x
   ```
3. 配置并构建，参考了[这里](http://blog.51cto.com/jinchuang/1897385)
   
   ```
   cd ../php-x.x.x
   ./configure \
   --prefix=/usr/local/php7 \
   --with-fpm-user=nginx \
   --with-fpm-group=nginx \
   --with-bz2 \
   --with-curl \
   --with-gd \
   --with-openssl \
   --with-mhash \
   --with-jpeg-dir \
   --with-png-dir \
   --with-freetype-dir \
   --with-iconv-dir=/usr/local/libiconv \
   --with-gettext \
   --with-libxml-dir \
   --with-zlib \
   --with-xmlrpc \
   --with-pcre-regex \
   --with-pear \
   --with-pdo-mysql=mysqlnd \
   --with-mysqli=mysqlnd \
   --with-libdir=lib64 \
   --enable-dom \
   --enable-xml \
   --enable-fpm \
   --enable-bcmath \
   --enable-ftp \
   --enable-sockets \
   --disable-ipv6 \
   --enable-mbregex \
   --enable-mbstring \
   --enable-calendar \
   --enable-static \
   --enable-fpm \
   --enable-bcmath \
   --enable-libxml \
   --enable-inline-optimization \
   --enable-mbregex \
   --enable-opcache \
   --enable-pcntl \
   --enable-shmop \
   --enable-soap \
   --enable-sockets \
   --enable-sysvsem \
   --enable-zip \
   ```

make & make install

```
4. 创建配置文件
```

cp php.ini-development /usr/local/php7/php.ini
cp /usr/local/php7/etc/php-fpm.conf.default /usr/local/php7/etc/php-fpm.conf
cp /usr/local/php7/etc/php-fpm.d/www.conf.default /usr/local/php7/etc/php-fpm.d/www.conf
// 方法1
cp -R sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
// 如果没有权限启动
cd /etc/init.d/php-fpm
chmod 755 php-fpm
//启动停止
/etc/init.d/php-fpm start|stop
// 方法2
cp php-7.2.1/sapi/fpm/php-fpm /usr/local/bin/
//启动停止
php-fpm | pkill php-fpm

```
5. 查看
```

~$ php -v
PHP 7.2.1 (fpm-fcgi) (built: Jan 30 2018 17:06:46)
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2017 Zend Technologies
```