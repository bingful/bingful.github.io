---
layout: post
title:  "Linux 服务管理"
date:   2013-11-12 12:38:00
categories: linux
tags: service linux
---

Ubuntu服务管理有很多需要我们掌握的命令，这么多的命令有不少需要我们学习的。

Redhat 提供了chkconfig这个命令来管理系统在不同运行级别下的服务

开启/关闭: 

	chkconfig ServiceName on/off 

并可以用chkconfig \-\-list(两个杠) 查看当前的制定状况。

Ubuntu服务管理里没有这个命令，其实也可以不用任何命令简单管理系统服务, 可以通过改变 

	/etc/rc*.d（*的取值是从0到6和S）

下的启动脚本名来管理服务. 比如不想让KDM自动启动, 可以这样: 

	sudo find /etc/rc* -name *kdm* -exec rm {} \; 

也就是把KDM的启动脚本全删掉.

Ubuntu服务管理也提供了另外一个简单的命令来实现管理。但首先服务必须已在/etc/init.d目录中存在。如：

添加一个服务: 

	sudo update-rc.d ServiceName defaults

删除一个服务: 

	sudo update-rc.d ServiceName remove

还可以安装另外一个比较强的工具: 

	sudo apt-get install sysv-rc-conf sysvconfig

启动: 

	sudo sysv-rc-conf

它可心配置各服务在各级别上的启动情况.

随时想启动某个服务, 可以这样: 

	sudo /etc/init.d/ServiceName start

比如我要远程登录, 要用ssh服务: 

	sudo /etcinit.d/ssh start (别的系统可能是sshd)

Ubuntu服务管理中还可以做别的操作:

	start : 启动服务
	stop : 停止服务
	restart : 关闭服务，然后重新启动
	reload : 使服不重新启动而重读配置文件
	status : 提供服务的当前状态
	condrestart : 如果服务锁定，则这个来关闭服务，然后再次启动。

再说一下 linux 运行级别的意思: 通常有这几个 runlevel : 

	runlevel　 
	system state
	0　 halt the system 
	1 　single user mode 
	2　 basic multi user mode 
	3　 multi user mode 
	5　 multi user mode with gui 
	6　 reboot the system 
	S 　single user mode 

runlevel 命令查看当前运行级别. init 命令改变当前运行级别.
