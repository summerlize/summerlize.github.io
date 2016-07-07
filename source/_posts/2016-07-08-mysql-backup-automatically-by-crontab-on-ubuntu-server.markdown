---
layout: post
title: "於 Ubuntu Server 使用 crontab 定時自動備份 mysql 資料庫"
date: 2016-07-08 01:02
comments: true
categories: [Ubuntu, Mysql]
---
使用指令 ``sudo vi /etc/crontab`` 編輯檔案

加入備份指令如下

	# 分 時 日 月 週 使用者 指令
	# 表示於每日12點整，以 root 身份備份 mysql 所有資料庫並存放到 /home/user/mysqldump/ 資料夾中
	0 0 * * * root mysqldump -u root -proot --all-databases > /home/user/mysqldump/database_`date '+\%Y-\%m-\%d'`.sql
	# 只備份指定的資料庫 somedb
	0 0 * * * root mysqldump -u root -proot somedb > /home/user/mysqldump/somedb_`date '+\%Y-\%m-\%d'`.sql

使用指令 ``sudo service cron restart`` 重啟 crontab 服務

---

相關文章：

[自動將 Ubuntu Server 日常排程(crontab)執行訊息(log)傳送至 Gmail 及 Authorication Failed 處理](/blog/2016/07/08/sending-log-email-by-crontab-daily-schedule-from-ubuntu-server/)

