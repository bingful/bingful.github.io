---
layout: post
title:  "树莓派折腾日记"
date:   2013-08-06 01:40:00
categories: linux
tags: lnmp raspbian
---

入手一块树莓派，这个小玩意体积小巧，却可以当做一台功能完整的计算机来使用。装系统，开始折腾啦。

## 安装系统

树莓派使用一块SD卡作为存储，系统可以在http://www.raspberrypi.org/downloads这个地址下载，我下载的是2013-07-26-wheezy-raspbian.zip。

解压缩，windows下面使用Win32DiskImager来将系统写到SD卡上面，而在Linux系统上面，这个步骤更简单了，一个命令就能搞定

	dd bs=1M if=~/downloads/2013-07-26-wheezy-raspbian.img of=/dev/sdc

其中/dev/sdc是指插入的SD卡，这个文件名千万要确认清楚了再进行，不然错写入数据盘，岂不悲剧。

将SD卡插入树莓，加电启动。我没有HDMI和DVI线，但是Raspbian系统默认是打开了ssh端口的，因此可以通过ssh登录到树莓进行操作。用一根网线将树莓和路由器连接起来，树莓加电一会儿之后，可以从路由器的控制页面“客户端列表”看到树莓设备，建议将树莓派的Mac地址分配一个静态IP，方便以后登录维护，一般路由器都有这个功能。

Windows下可以使用SecureCRT、putty工具登录树莓派，linux下直接：

	ssh pi@10.0.0.99

Raspbian默认的用户名：pi  默认密码：raspberry

## 配置树莓派

	sudo raspi-config

选择1 Expand Filesystem，可以将整块SD卡的空间充分利用。重启后，用df -h命令看吧，是不是整块SD卡的空间都能用了呢。其余的选项，改密码、设置Locale、超频等，都可以玩玩。

## 安装软件

安装一些常用的web服务端软件，我准备在树莓派上搭建一个小网站：

	sudo apt-get install nginx php5 php5-fpm mysql-server

php5好像默认安装了apache2，我把它的端口号改为了8080，免得和nginx冲突。

修改/etc/apache2/ports.conf，修改如下两行：

	NameVirtualHost *:8080
	Listen 8080

修改/etc/apache2/sites-enabled/000-default，将第一行改为

	<VirtualHost *:8080>

修改一下ngxin配置,让php起作用，vim /etc/nginx/sites-enabled/default，打开如下注释：

        location ~ \.php$ {
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini

                # With php5-cgi alone:
                #fastcgi_pass 127.0.0.1:9000;
                # With php5-fpm:
                fastcgi_pass unix:/var/run/php5-fpm.sock;
                fastcgi_index index.php;
                include fastcgi_params;
        }

并且将index指令修改为：

	index index.php index.html index.htm;

测试一下nginx配置：

	sudo /usr/sbin/nginx -t
	nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
	nginx: configuration file /etc/nginx/nginx.conf test is successful

可以看到nginx的配置是ok的

重启nginx和php5-fpm:

	sudo service nginx restart
	sudo service php5-fpm restart

创建一个php文件，vim /usr/share/nginx/www/index.php，输入以下内容：

	<?php phpinfo();

用浏览器访问  http://10.0.0.99/ 可以访问了！

done!

