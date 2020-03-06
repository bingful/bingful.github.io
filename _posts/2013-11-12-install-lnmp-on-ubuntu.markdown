---
layout: post
title:  "在 Ubuntu 13.10 安装 LNMP"
date:   2013-11-12 17:48:00
categories: linux
tags: lnmp ubuntu ubuntu13
---

## 安装mysql

0.下载源码 mysql-5.6.14.tar.gz

1.安装依赖

	sudo apt install gcc g++ libncurses5-dev make cmake autoconf bison automake

2.解压

	tar zxvf mysql-5.6.14.tar.gz && cd mysql-5.6.14

3.编译

	cmake \
	-DCMAKE_INSTALL_PREFIX=/server/mysql \
	-DMYSQL_DATADIR=/server/mysql/data \
	-DMYSQL_UNIX_ADDR=/server/mysql/mysqld.sock \
	-DDEFAULT_CHARSET=utf8 \
	-DDEFAULT_COLLATION=utf8_general_ci \
	-DWITH_EXTRA_CHARSETS:STRING=utf8,gbk \
	-DWITH_MYISAM_STORAGE_ENGINE=1 \
	-DWITH_INNOBASE_STORAGE_ENGINE=1 \
	-DWITH_MEMORY_STORAGE_ENGINE=1 \
	-DENABLED_LOCAL_INFILE=1 \
	-DMYSQL_USER=mysql 

4.make

5.安装

	sudo make install

6.参考 
   http://dev.mysql.com/doc/refman/5.5/en/installing-source-distribution.html  
   http://dev.mysql.com/doc/refman/5.5/en/postinstallation.html

7.服务管理

	shell> cp mysql.server /etc/init.d/mysql
	shell> chmod +x /etc/init.d/mysql
	shell> sudo update-rc.d mysql defaults

8.一些命令

修改MySQL root密码

	  ./bin/mysqladmin -u root password 'new-password'
	  ./bin/mysqladmin -u root -h bing-server password 'new-password'
	Alternatively you can run:
	  ./bin/mysql_secure_installation

启动Mysql服务

	  /bin/mysqld_safe &

测试MySQL daemon：

	  cd mysql-test ; perl mysql-test-run.pl


## 安装nginx

0.下载源码

    wget http://nginx.org/download/nginx-1.4.3.tar.gz
    wget http://www.openssl.org/source/openssl-1.0.1c.tar.gz
    wget http://sourceforge.net/projects/pcre/files/pcre/8.30/pcre-8.30.tar.gz/download
    wget http://zlib.net/zlib128.zip

1.解压

	tar zxvf ...
	unzip ...

2.编译配置

	   cd nginx-1.4.3
	   ./configure --prefix=/server/nginx --user=ngx --group=ngx --with-http_stub_status_module --with-http_flv_module --with-openssl=../openssl-1.0.1c --with-http_ssl_module --with-pcre=../pcre-8.30 --with-zlib=../zlib-1.2.8

3.编译 make

4.安装 sudo make install

5 在/etc/rc.local加上下面一行，用于开机启动

	/server/nginx/sbin/nginx &


## 安装php/fpm

0 下载php-5.5.5.tar.gz

1 sudo apt-get install libxml2-dev libcurl4-openssl-dev libncurses5-dev  libjpeg-dev libfreetype6-dev libpng12-dev libmcrypt-dev

2 安装bzip2

    wget http://www.bzip.org/1.0.6/bzip2-1.0.6.tar.gz
    tar zxf bzip2-1.0.6.tar.gz && cd bzip2-1.0.6
    make && sudo make install

3 tar zxf php-5.5.5.tar.gz && cd php-5.5.5

4 编译配置

	./configure --prefix=/server/php --enable-fpm --with-mcrypt --with-zlib --enable-mbstring --disable-pdo --with-curl --disable-debug --disable-rpath --enable-inline-optimization --with-bz2 --enable-sockets --enable-sysvsem --enable-sysvshm --enable-pcntl --enable-mbregex --with-mhash --enable-memcache --enable-zip --with-pcre-regex --with-mysql=/server/mysql  --with-mysqli=/server/mysql/bin/mysql_config --with-gd --without-pear

5 make

6 make test

7 sudo make install

8 FPM服务管理

	  sudo cp sapi/fpm/init.d.php-fpm /etc/init.d/fpm
	  sudo chmod a+x /etc/init.d/fpm
	  sudo update-rc.d fpm defaults

9 fpm启动 sudo /etc/init.d/fpm start 会有报错 貌似是cannot get gid of nobody。解决方法：
  sudo groupadd nobody

10 修改/server/nginx/conf/nginx.conf 去掉关于fastcgi的注释。注意下行的修改
   fastcgi_param  SCRIPT_FILENAME  /server/nginx/html/$fastcgi_script_name;

以上各软件安装后，需将各个bin sbin目录加入到/etc/profile的PATH环境变量中去.

done!