---
layout: post
title:  "在 CentOS 6.3 上安装 LNMP"
date:   2013-01-01 23:41:00
categories: linux
tags: lnmp centos centos6
---

2013年元旦，在出租屋折腾安装新系统以及安装LNMP环境，记录之。

## 安装 CentOS 6.3 系统

1、安装CentOS-6.3-x86_64-minimal.iso。推荐网易的镜像下载。http://mirrors.163.com/centos/6.3/isos/x86_64/CentOS-6.3-x86_64-minimal.iso

安装过程就不多说了。环境：	VmWare Workstation 9.0 双核CPU 1G内存 60G硬盘 Birdge模式网卡。其中配置了路由器的dhcp服务，分配给该虚拟机固定的ip：10.0.0.22。

2、在虚拟机中启动centos，登录root用户，添加工作用户并设置用户密码

	groupadd bing
	useradd –g bing bing
	passwd bing

3、编辑/etc/sysconfig/network-scripts/ifcfg-eth0 ，修改ONBOOT，重启即可联网

	ONBOOT="yes"

4、使用ifconfig查看IP，然后使用service sshd status查看sshd服务的状态

5、使用终端如SecureCRT登录centos

	[bing@centos ~]$ uname -a
	Linux centos.centos-d 2.6.32-279.el6.x86_64 #1 SMP Fri Jun 22 12:19:21 UTC 2012 x86_64 x86_64 x86_64 GNU/Linux
	[bing@centos ~]$ date
	2013年 01月 01日 星期二 11:24:33 CST

## 安装必要的系统工具和软件

1．minimal版的CentOS 6.3所安装的系统软件和工具很少，需要自己一一安装。为什么选择使用minimal呢，因为自己动手安装系统工具和软件也是一个必要的学习过程

	su	#切换到root用户
	yum list installed #查看安装了那些软件
	yum install sudo wget #安装sudo wget工具
	yum group install “Development Tools”	#安装开发工具、编译器

将bing用户添加到sudo用户中，编辑/etc/sudoers，增加一行

	vi /etc/sudoers
	bing    ALL=(ALL)       ALL

2.安装nginx

下载nginx/openssl/pcre/zlib

	cd ~/downloads
	wget http://nginx.org/download/nginx-1.2.6.tar.gz
	wget http://www.openssl.org/source/openssl-1.0.1c.tar.gz
	wget http://sourceforge.net/projects/pcre/files/pcre/8.30/pcre-8.30.tar.gz/download
	wget http://zlib.net/zlib127.zip

解压之

	tar zxvf …
	unzip …

安装nginx

	sudo mkdir /server	#安装的软件都装在这个目录
	cd nginx-1.2.6
	./configure --prefix=/server/nginx --user=nginx --group=nginx --with-http_stub_status_module --with-http_flv_module --with-openssl=../openssl-1.0.1c --with-http_ssl_module --with-pcre=../pcre-8.30 --with-zlib=../zlib-1.2.7 #只需要制定openssl、pcre、zlib的源代码位置即可
	make
	su #切换到root用户
	make install

测试nginx是否安装成功

	cd /server/nginx/sbin
	sudo ./nginx #启动nginx
	vi /etc/sysconfig/iptables #修改防火墙，允许80端口通信，增加一行：-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
	sudo service iptables #重启iptables

在浏览器输入10.0.0.22，测试之，正常的话就出现nginx默认的开始页面了。我仿照yum安装nginx后生成的/etc/init.d/nginx文件写了一个类似的启动文件，然后

	chkconfig nginx on

之后系统启动的时候nginx就能作为服务一起启动了。

3．安装MySQL（参考了博客：http://blog.csdn.net/yeno/article/details/8053571）

首先安装依赖：

	sudo yum install cmake make gcc gcc-c++ autoconf bison automake zlib* fiex* libxml* ncurses-devel libmcrypt* libtool-ltdl-devel* -y

去官网下载了mysql-5.5.29.tar.gz

	wget http://124.254.47.36/download/34538382/45313441/6/gz/39/57/1356429338919_825/mysql-5.5.29.tar.gz

解压，进入目录，配置：

	cmake \
	        -DCMAKE_INSTALL_PREFIX=/server/mysql \
	        -DMYSQL_UNIX_ADDR=/server/mysql/data/mysql.sock \
	        -DEXTRA_CHARSETS=all \
	        -DDEFAULT_CHARSET=utf8 \
	        -DDEFAULT_COLLATION=utf8_general_ci \
	        -DWITH_EXTRA_CHARSETS:STRING=utf8,gbk \
	        -DWITH_INNOBASE_STORAGE_ENGINE=1 \
	        -DWITH_READLINE=1 \
	        -DENABLED_LOCAL_INFILE=1 \
	        -DMYSQL_DATADIR=/server/mysql/data \
	        -DMYSQL_TCP_PORT=3306 #注释：/server/mysql是安装位置，/server/mysql/data是数据文件存放位置
	sudo make && make install

检查用户是否建立：

	cd /server/mysql
	cat /etc/passwd | grep mysql
	cat /etc/group | grep mysql		#结果是没有看到有mysql用户建立

故自己新建mysql用户

	groupadd mysql
	useradd -g mysql mysql -s /sbin/nologin

文件权限设置：

	cd /server/mysql/
	chown mysql.mysql -R .

设置配置文件：

	cp support-files/my-medium.cnf /etc/my.cnf

安装数据库：

	chmod 755 scripts/mysql_install_db
	scripts/mysql_install_db --user=mysql --basedir=/server/mysql --datadir=/server/mysql/data

通过执行mysql_install_db可以获得接下来的操作步骤信息：

	[root@centos mysql]# scripts/mysql_install_db --user=mysql --basedir=/server/mysql --datadir=/server/mysql/data
	Installing MySQL system tables...
	OK
	Filling help tables...
	OK

	To start mysqld at boot time you have to copy
	support-files/mysql.server to the right place for your system

	PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
	To do so, start the server, then issue the following commands:

	/server/mysql/bin/mysqladmin -u root password 'new-password'
	/server/mysql/bin/mysqladmin -u root -h centos.centos-d password 'new-password'

	Alternatively you can run:
	/server/mysql/bin/mysql_secure_installation

	which will also give you the option of removing the test
	databases and anonymous user created by default.  This is
	strongly recommended for production servers.

	See the manual for more instructions.

	You can start the MySQL daemon with:
	cd /server/mysql ; /server/mysql/bin/mysqld_safe &

	You can test the MySQL daemon with mysql-test-run.pl
	cd /server/mysql/mysql-test ; perl mysql-test-run.pl

	Please report any problems with the /server/mysql/scripts/mysqlbug script!

mysqld服务开机自启动配置

	cp support-files/mysql.server /etc/init.d/mysqld
	chmod 755 /etc/init.d/mysqld
	chkconfig mysqld on
	chkconfig --list | grep mysqld

启动mysqld服务：

	service mysqld start
	service mysqld status

设置root用户密码：

	/server/mysql/bin/mysqladmin -u root password '******'

本机登录mysql，不用密码，需要先设置PATH，我采用的是在系统PATH中设置到/server/mysql/bin/mysql的软链接

	cd /usr/local/bin
	ln -sf /server/mysql/bin/mysql
	cd /usr/local/sbin
	ln -sf /server/mysql/bin/mysql
	mysql 	#本机登录mysql，不需要密码
	show databases;		#看不到mysql数据库

退出无密码登录的mysql会话，然后用密码登录

	mysql -u root –p
	show databases 	#可以看到有mysql数据库
	use mysql		#选择mysql数据库
	show tables;
	delete from user where password='';	#删去无密码的用户
	flush privileges;
	grant all privileges on *.* to root@'%' identified by "root";	#使root用户能从其他机器上远程登录
	flush privileges;

查看此时mysql服务器上可以登录的用户，可以看到root用户从localhost和从%登录，其密码是用的不一样的。

	select user, password, host from users;mysql> select user, password, host from user;
	+------+-------------------------------------------+-----------+
	| user | password                                  | host      |
	+------+-------------------------------------------+-----------+
	| root | *E33A9C0F160EDB4646BF6D162D9A03028F785466 | localhost |
	| root | *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B | %         |
	+------+-------------------------------------------+-----------+
	2 rows in set (0.01 sec)

退出mysql,再试用单个的mysql命令无密码登录就行不通了。

4．安装PHP 5.3.20和设置fpm

安装依赖：

	yum install bzip2 bzip2-devel curl curl-devel libjpeg libjpeg-devel libpng libpng-devel freetype-devel –y

去SourceForge下载并安装libmcrypt:

	tar zxvf libmcrypt-2.5.8.tar.gz
	cd libmcrypt-2.5.8
	./configure
	sudo make && make install 

下载php-5.3.20.tar.gz,然后

	tar zxvf php-5.3.20.tar.gz
	cd php-5.3.20
	./configure --prefix=/server/php --enable-fpm --with-mcrypt --with-zlib --enable-mbstring --disable-pdo --with-curl --disable-debug --enable-pic --disable-rpath --enable-inline-optimization --with-bz2 --with-xml --with-zlib --enable-sockets --enable-sysvsem --enable-sysvshm --enable-pcntl --enable-mbregex --with-mhash --enable-xslt --enable-memcache --enable-zip --with-pcre-regex --with-mysql=/server/mysql  --with-mysqli=/server/mysql/bin/mysql_config --with-zlib_dir=/usr/lib64 --with-gd --without-pear
	ln -sf /server/mysql/lib/libmysqlclient.so.18 /usr/lib64/	#重要，否则中间会报错，找不到libmysqlclient.so.18
	make
	make test #此处花时间较长
	sudo make install

设置php可执行文件路径

	cd /usr/local/bin
	ln –sf /server/php/bin/php
	cd ../sbin
	ln –sf /server/php/bin/php

设置fpm服务：

	cd sapi/fpm
	sudo cp php-fpm.conf /server/php/etc/php-fpm.conf	  #fpm配置文件
	sudo cp init.d.php-fpm /etc/init.d/fpm
	sudo chmod a+x /etc/init.d/fpm 
	sudo chkconfig fpm on
	chkconfig
	sudo service fpm start	#启动fpm

修改nginx配置文件	

	vi /server/nginx/conf/nginx.conf

去掉关于fastcgi的注释，注意/server/nginx/html/（/server/nginx/html/这个地方要修改，就是这个让我浪费了很多时间 ==）：

        location ~ \.php$ {
            root           html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /server/nginx/html/$fastcgi_script_name;
            include        fastcgi_params;
        }

写个测试文件测试一下

	vi /server/nginx/html/phpinfo.php

内容如下：

	<?php phpinfo();

浏览器访问 http://10.0.0.22/phpinfo.php ,可以看到输出了php的服务器环境信息。

done!

