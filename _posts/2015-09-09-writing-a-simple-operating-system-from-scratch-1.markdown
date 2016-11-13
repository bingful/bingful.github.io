---
layout: post
title:  "【译】《从零开始写一个简单的操作系统》 - 第一章 引言"
date:   2015-09-09 00:09:00
categories: osdev
tags: os 操作系统 assembly 汇编 bootloader 
#published: false
---

## 目录 ##

[【译】《从零开始写一个简单的操作系统》 - 译序][ch0]

[【译】《从零开始写一个简单的操作系统》 - 第一章 引言][ch1]

[【译】《从零开始写一个简单的操作系统》 - 第二章 计算机体系结构和启动过程][ch2]

[【译】《从零开始写一个简单的操作系统》 - 第三章 16位实模式下启动扇区程序设计][ch3]

[【译】《从零开始写一个简单的操作系统》 - 第四章 切换到32位保护模式][ch4]

[【译】《从零开始写一个简单的操作系统》 - 第五章 编写、编译并加载内核][ch5]

[【译】《从零开始写一个简单的操作系统》 - 第六章 开发必要的设备驱动和文件系统][ch6]

作者：Nick Blundel

译者：祁冰

我们都用过操作系统，例如Windows XP、Linux等。有许多人还编写过在其上运行的程序。那么，究竟什么是操作系统？当我们使用电脑时，我们所能看到的功能，哪些是由硬件提供，哪些是由软件提供？计算机的运作原理是怎样的？

已故的Doug Shepherd教授，是我在兰斯特大学的老师，他是个活泼的人。他有一次在我抱怨一些恼人的程序问题时提醒我，在过去他开始做学术研究之前，他必须得亲自从头开始写操作系统来给自己用。现如今，我们似乎理所当然的认为，这些每美妙的机器是在各层软件的控制之下运行的，软件和硬件绑定在一起，硬件日复一日的运行都离不开软件。

现在，我们把目光集中到广泛应用的x86架构CPU，我们将深入计算机内部，沿着Doug教授早期的足迹，一路探寻：

* 计算机怎样启动

* 如何写一个能在没有操作系统的『贫瘠』环境上运行的底层程序

* 怎样配置CPU来使用它的扩展功能

* 怎样运行高级语言写的代码块，这样我们才能朝编写操作系统的目标更近一步

* 怎样创建基本的操作系统服务，比如设备驱动、文件系统、多任务处理

注意，关于实用的操作系统功能，这份教程不打算展开，而是专注于将从各种渠道和资源汇聚而来的信息碎片整合为一个自给型的、连贯的文档，本教程会带给你底层编程的实战体验，体验操作系统如何编写，弄明白它解决了什么问题。这份教程手法独特，因为它使用的语言和工具（例如汇编、C语言、Make工具等）不是我们所关心的核心，而是达到目标的手段：我们将学习这些语言和工具中我们需要的部分来帮助实现我们的主要目标。

这份教程定位于操作系统开发的敲门砖，而非替代譬如优秀的[Minix Project][minix3]和通用的操作系统开发。


[jobbole]:      http://top.jobbole.com/13810/
[minix3]:       http://www.minix3.org/
[ch0]:          /osdev/writing-a-simple-operating-system-from-scratch-0
[ch1]:          /osdev/writing-a-simple-operating-system-from-scratch-1
[ch2]:          /osdev/writing-a-simple-operating-system-from-scratch-2
[ch3]:          /osdev/writing-a-simple-operating-system-from-scratch-3
[ch4]:          /osdev/writing-a-simple-operating-system-from-scratch-4
[ch5]:          /osdev/writing-a-simple-operating-system-from-scratch-5
[ch6]:          /osdev/writing-a-simple-operating-system-from-scratch-6
