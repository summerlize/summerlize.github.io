---
layout: post
title: "自動將 Ubuntu server 日常排程(crontab)執行訊息(log)傳送至 Gmail 及 Authorication Failed 處理"
date: 2016-07-08 01:42
comments: true
categories: [Ubuntu] 
---

### step1: 下載 ssmtp

``$ sudo apt-get update && echo y | sudo apt-get install ssmtp mailutils``
	
### step2: 編輯 ssmtp.conf 檔

``$ sudo rm /etc/ssmtp/ssmtp.conf && sudo nano /etc/ssmtp/ssmtp.conf``

將下方內容貼至 ssmtp.conf 檔案中

	# Config file for sSMTP sendmail
	# DONT CHANGE BELOW FOR @GMAIL.COM
	mailhub=smtp.gmail.com:587
	rewriteDomain=gmail.com
	hostname=localhost
	FromLineOverride=YES
	UseTLS=Yes
	UseSTARTTLS=Yes
	
	# CHANGE NEXT 3 OPTIONS 將下方三行改為自己的帳號密碼
	# Username/Password (NO @Gmail)
	root=yourusername@gmail.com
	AuthUser=yourusername
	AuthPass=yourpassword

### step3: 編輯 revaliases 檔

``$ sudo nano /etc/ssmtp/revaliases``

將下方內容貼至 revaliases 檔案中

	# sSMTP aliases
	# linux user:mail:stmp port
	# 將 ubuntu 中的使用者 (user) 對應其 email
	root:yourusername@gmail.com:smtp.gmail.com:587
	guest:somebody@gmail.com:smtp.gmail.com:587

### step4: 測試是否可寄送 email

``$ ssmtp yourusername@gmail.com``

輸入上方指令按下 enter，貼上下方段落 email 內容。

note: Subject 之後一定要空行

	To: yourusername@gmail.com
	From: yourusername@gmail.com
	Subject: test

	Hello World!

完成後使用 ``ctrl+d`` 跳出，至信箱檢查 email。

也可使用指令 ``echo "Hello world mail content" | mail -s "Hello world Subject" yourusername@gmail.com`` 測試寄信。

### 若顯示錯誤訊息 Authorization failed (534 5.7.14 https://support.google.com/mail/answer/78754 tm1sm6036157pac.23 - gsmtp

首先到 [https://www.google.com/settings/u/1/security/lesssecureapps](https://www.google.com/settings/u/1/security/lesssecureapps) 將 ``安全性較低的應用程式存取權限`` 設為 ``開啟``

再試試 step4 ，若還是一樣顯示錯誤，則可試第二種方法，

下載文字瀏覽器 w3m，

``$ sudo apt-get install w3m w3m-img``

連結至 gmail 頁面，並輸入帳號密碼登入，

``$ w3m mail.google.com``

初次登入 gmail 會要求輸入備援電話號碼之驗證碼或備援電子信箱，

登入成功後再重新試 step4。

### step5: 使日常排程將 log 自動發送至 email

前面測試寄送 email 成功後，使用指令編輯 crontab，

``$sudo vi /etc/crontab``

在 crontab 中 PATH 下一行加入

	MAILTO=yourusername@gmail.com