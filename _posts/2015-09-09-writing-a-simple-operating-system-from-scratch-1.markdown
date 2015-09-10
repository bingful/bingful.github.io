---
layout: post
title:  "【译】Chapter 1 Introduction"
date:   2015-09-09 00:09:00
categories: osdev
tags: os 操作系统 assembly 汇编 bootloader 
#published: false
---

### 目录 ###

[【译】Chapter 1 Introduction][ch1]

[【译】Chapter 2 计算机结构和启动过程][ch2]

[【译】Chapter 3 启动扇区编程（在16位实模式环境下）][ch3]

[【译】Chapter 4 进入32位保护模式][ch4]

[【译】Chapter 5 内核的编写、构建和加载][ch5]

[【译】Chapter 6 开发必需的设备驱动和文件系统][ch6]

作者：Nick Blundel

译者：祁冰

我们都曾用过操作系统，例如Windows XP、Linux等等，而且我们可能还在上面编写运行过程序！但是究竟什么是操作系统呢？当我们用电脑时，我们所看到的功能有多少是硬件完成的，又有多少是软件负责的呢？电脑到底是怎样跑起来的？

已故的Doug Shepherd教授，是我在兰斯特大学的老师，他是个活泼的人。他有一次在我抱怨一些恼人的程序问题时提醒我，在过去，他开始做学术研究之前，他必须得自己从0开始写操作系统来给自己用。现如今，我们似乎理所当然的认为，这些每美妙的机器是在各层软件的控制之下运行的，软件和硬件绑定在一起，硬件日复一日的运行都离不开软件。

现在，我们把目光聚焦到广泛应用的x86架构CPU，我们将把电脑的软件外衣剥个干净，沿着Doug教授早期的足迹，一路探寻：

* 计算机怎样启动

* 如何写一个底层的程序，跑在操作系统加载之前的“贫瘠”环境上

* 怎样配置CPU，让我们能用上它的扩展功能

* 怎样启动到高级语言写的代码，这样我们真正能朝我们的操作系统目标前进了

* 怎样创建一些基本的操作系统服务，比如设备驱动、文件系统、多任务处理

请注意，至于实际的操作系统功能，这份教程不打算在这方面展开，而是专注于将从各种渠道和资源而来的信息碎片聚合为一个自给型的、连贯的文档，会带给你底层编程的动手体验，体验操作系统是如何编写，弄明白它解决了什么问题。这份教程手法独特，因为它使用的语言和工具（例如汇编、C语言、Make工具等）不是所关心的核心，而是达到目标的手段：我们将学习这些语言和工具中我们需要的部分来帮助实现我们的主要目标。

这个项目定位于敲门砖，而非替代譬如优秀的[Minix Project][minix3]和通用的操作系统开发。


[ch1]:          /osdev/writing-a-simple-operating-system-from-scratch-1/
[ch2]:          /osdev/writing-a-simple-operating-system-from-scratch-2/
[ch3]:          /osdev/writing-a-simple-operating-system-from-scratch-3/
[ch4]:          /osdev/writing-a-simple-operating-system-from-scratch-4/
[ch5]:          /osdev/writing-a-simple-operating-system-from-scratch-5/
[ch6]:          /osdev/writing-a-simple-operating-system-from-scratch-6/
[minix3]:       http://www.minix3.org/
