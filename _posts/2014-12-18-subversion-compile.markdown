---
layout: post
title:  "Subversion 1.7.17编译并支持http/https schema"
date:   2014-12-18 03:10:00
categories: linux
tags: linux subversion
---
CentOS 6.6编译subversion 1.7.17并支持http/https schema

Mac自带的svn版本是1.7.17, 虚拟机centos 6.6采用Yum安装的svn版本低很多。两个系统之间有一个共享web/htdocs目录，svn版本号不一样导致代码版本管理不方便。

因此决定将虚拟机centos 6.6系统svn版本升级到1.7.17

1.卸载centos的svn，以及安装相关依赖

{% highlight bash %}
yum remove subversion
yum groupinstall "Development Tools"
yum install openssl openssl-devel libxml2-devel expat-devel
{% endhighlight %}

2.下载subversion 1.7.17源代码

{% highlight bash %}
wget http://archive.apache.org/dist/subversion/subversion-1.7.17.tar.gz
{% endhighlight %}

3.下载依赖的apr、apr-util、neon、serf、sqlite

{% highlight bash %}
tar zxvf subversion-1.7.17.tar.gz
cd subversion-1.7.17
./get-deps.sh
{% endhighlight %}

执行完get-deps.sh之后在当前目录下就多了以上依赖的几个安装包的目录

4.安装依赖包

注意，如果这个时候就执行./configure && make && make install的话，编译出来的svn只能支持svn和file这样的schema，不支持http和https。那就分别安装以上依赖包吧。

{% highlight bash %}
cd apr
./configure --prefix=/usr/local/apr
make && make install

cd ../apr-util
./configure --prefix=/usr/local/apr-util --with-apr=/usr/local/apr
make && make install

cd ../neon
./configure --prefix=/usr/local/neon --with-ssl
make && make install

cd ../serf
./configure --prefix=/usr/local/serf --with-apr=/usr/local/apr --with-apr-util=/usr/local/apr-util --with-openssl=/usr
make && make install
cd ..
{% endhighlight %}

5.编译安装subversion, sqlite会在这个步骤被编译

{% highlight bash %}
./configure --prefix=/usr/local/subversion --with-apr=/usr/local/apr --with-apr-util=/usr/local/apr-util --with-neon=/usr/local/neon --with-serf=/usr/local/serf --with-ssl --with-openssl
make && make install
{% endhighlight %}

6.将subversion安装目录/bin添加到系统PATH

编辑/etc/profile在最后添加一行   export PATH=$PATH:/usr/local/subversion/bin

{% highlight bash %}
[root@nut subversion-1.7.17]# source /etc/profile
[root@nut subversion-1.7.17]# echo $PATH
/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/subversion/bin:/root/bin
{% endhighlight %}


7.大功告成啦，执行一下svn \-\-version看看。

{% highlight bash %}
[root@nut subversion-1.7.17]# svn --version
svn，版本 1.7.17 (r1591372)
   编译于 Dec 18 2014，03:05:23

Copyright (C) 2014 The Apache Software Foundation.
This software consists of contributions made by many people; see the NOTICE
file for more information.
Subversion is open source software, see http://subversion.apache.org/

可使用以下的版本库访问模块:

* ra_neon : 通过 WebDAV 协议使用 neon 访问版本库的模块。
  - 处理“http”方案
  - 处理“https”方案
* ra_svn : 使用 svn 网络协议访问版本库的模块。  - 使用 Cyrus SASL 认证
  - 处理“svn”方案
* ra_local : 访问本地磁盘的版本库模块。
  - 处理“file”方案
* ra_serf : 通过 WebDAV 协议使用 serf 访问版本库的模块。
  - 处理“http”方案
  - 处理“https”方案
{% endhighlight %}

