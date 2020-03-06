---
layout: post
title:  "怎样使用 patch"
date:   2014-10-20 02:37:00
categories: linux
tags: patch
---

生成 patch 文件

	diff -u oldfile newfile > patch_from_old_to_new.txt

应用 patch 文件，以下操作会立即修改 oldfile

	patch -p1 oldfile patch_from_old_to_new.txt