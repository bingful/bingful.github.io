---
layout: post
title:  "CURL命令用法备忘"
date:   2014-05-13 00:00:00
categories: linux
tags: linux curl
---
1.查看response body

{% highlight bash %}
$ curl www.sina.com
{% endhighlight %}

2.查看response header

{% highlight bash %}
$ curl -I www.sina.com
{% endhighlight %}

3.查看response header 和 body

{% highlight bash %}
$ curl -i www.sina.com
{% endhighlight %}

4.自动跳转

{% highlight bash %}
$ curl -L www.sina.com
{% endhighlight %}

5.显示通信过程

{% highlight bash %}
$ curl -v www.sina.com
$ curl --trace output.txt www.sina.com(更详细，二进制)
$ curl --trace-ascii output.txt www.sina.com(更详细，文本格式)
{% endhighlight %}

6.POST请求

{% highlight bash %}
$ curl --data "data=xxx" www.sina.com
$ curl --data-urlencode "data=xxx" www.sina.com
{% endhighlight %}

7.设置请求方式 -X

{% highlight bash %}
$ curl -X POST www.sina.com
$ curl -X DELETE www.sina.com
{% endhighlight %}

8.文件上传

{% highlight bash %}
$ curl --form upload=@/tmp/path/to/file.ext --form press=OK [URL]
{% endhighlight %}

9.指定Referer  \-\-referer

10.指定UA      \-\-user-agent

11.设置cookie  \-\-cookie

12.增加头信息   \-\-header "Content-Type:application/json"

{% highlight bash %}
$ curl --header "Content-Type:application/json" http://example.com
{% endhighlight %}

13.HTTP认证 \-\-user

{% highlight bash %}
$ curl --user name:password example.com
{% endhighlight %}



