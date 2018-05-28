---
layout: post
title: "macOS: High Sierra / brew 安裝問題 link Permission denied"
date: 2018-05-22 13:44
comments: true
categories: macOS/OSX
---

使用 brew 安裝 python 時出現錯誤，
	
	You can try again using `brew link gdbm`

再重新安裝顯示已安裝成功，

	Warning: python 3.6.5 is already installed, it's just not linked
	You can use `brew link python` to link this version.
	
執行``$ brew link python``出現錯誤，

	Linking /usr/local/Cellar/python/3.6.5... Error: Permission denied @ dir_s_mkdir - /usr/local/Frameworks

解決方法：

	$ sudo mkdir /usr/local/Frameworks
	$ sudo chown $(whoami):admin /usr/local/Frameworks
	$ brew link python
	Linking /usr/local/Cellar/carthage/0.28.0... 4 symlinks created
