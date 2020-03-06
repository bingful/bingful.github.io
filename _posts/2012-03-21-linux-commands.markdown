---
layout: post
title:  "Linux 常用命令和 vim 命令"
date:   2012-03-21 20:43:00
categories: linux
tags: linux vim
---

2012年3月21日 20:43 学习 Linux 常用命令并记录之。

## 常用命令

	rz	接收文件
	sz	发送文件

	cp	拷贝文件
	cp -r	拷贝文件夹
	rm -r	删除文件
	rm -r	删除文件夹
	mv	移动
	*find -name=文件名	查找文件


	cat	仅显示文件内容
	more（less）	只显示一屏，空格继续翻页
	head -n	查看文件的前n行（n默认：10）
	tail -n 查看文件的后n行（n默认：10）

	grep 查找文件内容
	cut	截取文件的某一列（可以指定分隔符，默认分隔符：\t）
	sort	对文件内容按列排序 sort -t ' ' -k1,1 -k2,2rn 
	uniq	对文件按行去重

	vimdiff	比较文件差异
	ln -sf	建立软链接
	top 查看系统资源使用情况
	ps	查看进程,常见：ps -afx,ps -ef,ps -aux

	touch	改变文件或目录时间为最新
	whereis	查找一个可执行文件（命令）所有路径
	which	命令的实际路径

	wc	计算文件字数或行数
	adduser/useradd	添加新用户
	passwd	改变用户密码
	date	显示或设置系统时间
	free	显示内存剩余状态
	halt	关机
	reboot	重启
	kill	杀进程（-9：强制杀）
	su	变更用户身份
	chmod	改变文件权限 chmod (u|g|o|a)(+|-)(r|w|x) 
	chown	改变文件所有权

	ifconfig	查看ip
	netstat	-anp	显示网络状态
	telnet	远程登录

	df	查看剩余空间
	du	显示目录或文件的大小
	pwd	显示当前目录
	tree	以树状图列出目录的内容

	fdisk	查看磁盘情况

	tar -cvf打包；-xvf解压缩；-czvf压缩打包
	rar x解压缩


## vim 命令

	ESC 回到命令模式
	i,o, a 回到插入模式
	:w	保存
	:wq	保存退出
	:q!	不保存退出
	ZZ	同wq功能
	:n	定位到第n行
	nG	定位到第n行
	shift+g	定位到文件尾
	G [[ 定位到文件尾
	1G ]] 定位到文件头

	shift+v	选中1或多行
	crtl+v	选中1或多列
	ctrl+v shift+i 编辑 ESQ 多行编辑
	[n]dd	删除/剪切[n行，n默认1]
	[n]yy	拷贝[n行，n默认1]
	[n]p	粘贴[n次，n默认1]
	/关键词[\c]	查找[忽略大小写]
	:m,ns/原关键词/目标关键词/[g全部]; %s表示所有行 替换
	u	撤销
	crtl+r	恢复
	shift+u	大写（先shift+v选中行）
	u	小写（先shift+v选中行）
	:set nu	显示行号
	:set nonu	取消显示行号
	:set list	显示\n和\t
	:set nolist	取消显示\n和\t
	Ctrl+N 自动补全 (在插入模式)
	Ctrl+P 自动补全 (在插入模式)
	% 定位到匹配的(,[,{
	= 自动缩进
	:retab 按vim配置重新缩进
	编辑模式 ctrl+v， 输入tab  设置了expandtab后输入tab
	上下左右箭头,HOME,END,PAGEUP,PAGEDOWN在(命令和插入模式下都能用)
	Shift+Insert 粘贴(在vi和终端上都能用)

## 网络配置

1、配置ip

查看ip:ifconfig 

配置ip:

打开/etc/network/interfaces ,如下编辑，示例:

	动态ip
	auto eth0
	iface eth0 inet dhcp

	静态ip
	auto eth1
	iface eth1 inet static
	  address 192.168.136.83
	  gateway 192.168.136.1
	  netmask 255.255.255.0

重启网络

	 /etc/init.d/networking restart

手工禁用某个网卡	 

	ifconfig eth0 down

手工启用某个网卡	 

	ifconfig eth0 up

2、配置hosts

	vi /etc/hosts	

3.配置dns

	vi /etc/resolv.conf	
