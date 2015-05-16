---
layout: post
title:  "Memcached的协议和命令"
date:   2015-03-08 00:00:00
categories: linux
tags: memcached memcache
---

参考文档： memcached源码/doc/protocal.txt

Memcached客户端和服务端之间的通讯协议是基于TCP或UDP之上的简单文本协议，在阅读了源码中的protocal.txt之后，对mc的操作有了更清楚的认识，在此记录以作备忘。

## TCP协议接口 ##

### 存储命令 ###

    <command name> <key> <flags> <exptime> <bytes> [noreply]\r\n
    cas <key> <flags> <exptime> <bytes> <cas unique> [noreply]\r\n


* \<command name\>可以是："set", "add", "replace", "append" or "prepend"

     set: 存储

     add: 仅在key不存在的情况下存储

     replace: 仅在key存在的情况下存储

     append: 将数据追加到**存在的**key的数据后面

     prepend: 将数据前置到**存在的**key的数据前面

     **cas**: 存储数据，仅在自上次获取该数据之后 数据没被修改的情况下

*     key的最大长度是250；

*     exptime为0表示不过期，不超过60x60x24x30(30天的秒数)情况下表示当前服务器时间的偏移值，否则被解读为unix时间戳；

*     flags 任意16位无符号整数，服务器存储之，并随get命令返回，客户端可以用它来存储特定的比特位信息，对服务端不透明。memcached版本1.2.1或更高时此值为32位，但是为了兼容老版本，最好存储16位数据

*     cas unique 对某个已存储数据来说是唯一的64位识别符，随gets命令返回

*     noreply ： 如果包含noreply，则指示服务器不要发送响应，但是如果请求命令格式错乱，则服务器还是会返回响应数据。客户端应该只发送合法的命令.

### 获取命令 ###

    get key*\r\n
    gets key*\r\n

key\* :  空格间隔的多个key列表，gets命令会返回\<cas unique\>

返回值如下

    VALUE <key> <flags> <bytes> [<cas unique>]\r\n
    <data block>\r\n

### 删除命令 ###

    delete <key> [noreply]\r\n

### 增/减命令 ###

    incr <key> <value> [noreply]\r\n
    decr <key> <value> [noreply]\r\n

key对应的数据被当做64位无符号值，key的值必须存在，不存在的情况下incr/decr命令报错。

下溢：若试图将值设置到0以下，新的值将会被设为0；

上溢：Overflow in the "incr" command will wrap around the 64 bit mark.（啥意思？）

### touch命令 ###

touch:在不获取数据的情况下 修改已存在的数据的超时值。

    touch <key> <exptime> [noreply]\r\n

### 统计命令 ###

    stats\r\n
    stats <args>\r\n

args例如:

*          stats settings
*          stats items
*          stats sizes
*          stats slabs
*          stats conns
          
### 其他命令 ###

*flush_all*命令让所有数据都失效，可以带一个延迟失效的时间*delay_seconds* ，用于多个服务器依次间隔一段时间失效以避免同时失效造成DB负载突然升高。

    flush_all [delay_seconds]\r\n    

查看服务器版本号

    version\r\n

设置服务器日志啰嗦级别，0 = none, 1 = some, 2 = lots

    verbosity 0|1|2

服务器收到此命令后立即断开连接。客户端不需要连接的时候，可以主动断开，无需发送quit

    quit\r\n   


## UDP协议接口 ##

udp数据报包含一个简单的frame header，后接TCP协议一样的数据格式。

frame header长度8个字节，包含4个值，每个值都是16位整数，网络字节序，high byte first。

* 0-1 Request ID
* 2-3 Sequence number
* 4-5 Total number of datagrams in this message
* 6-7 Reserved for future use; must be 0

Request ID是客户端选取的任意值，一般是从一个随机种子开始的单调递增值。服务器的response会包含同样的ID.客户端用RequestID来区别不同请求的响应。

Sequnce Number是0至n-1，n是该消息的总共的数据报个数，即第三个值。客户端用顺序号拼接正确的响应数据，响应数据和TCP响应格式相同。



