---
layout: post
title: "同時使用多個 GitHub 帳號與多個 SSH 認證金鑰之設定方式"
date: 2016-05-21 10:53
comments: true
categories: [Git, GitHub]
---

現實中我們可能希望 GitHub 的私人與工作帳號能有所區分，

假設私人帳號為 personal 、工作帳號為 work ，步驟如下：

###Step1

需產生兩組不同的 ssh ，如原先已設定好 ssh 則可跳過此步驟。

首先產生私人帳號 personal 的金鑰，

	$ ssh-keygen -t rsa -C "personal@mail.com"
	Generating public/private rsa key pair.
	Enter file in which to save the key (/Users/username/.ssh/id_rsa): 
	
<font color="#1E90FF">~/.ssh/id_rsa_personal</font>

	Enter passphrase (empty for no passphrase):
	Enter same passphrase again:

<font color="#1E90FF">輸入密碼</font>

	Your identification has been saved in ~/.ssh/id_rsa_personal.
	Your public key has been saved in ~/.ssh/id_rsa_personal.pub.
	The key fingerprint is:
	SHA256:RP68Sn3j79q8U2h80cpifw3WKNTDn7b6aw8YDLbJ9Uo personal@mail.com

接著將 `~/.ssh/id_rsa_personal.pub` 中的金鑰複製起來，

再到 GitHub 	點選右上角帳號圖像，選 Settings ， 進入 SSH and GPG Keys ，

點選 New SSH Key ，在 title 輸入方便自己辨識的名稱，例如 homeComputer-personal ssh ，

並將剛才複製的金鑰貼在 key 的區塊，再按下 Add SSH Key 按鈕完成。


再使用指令 `ssh-keygen -t rsa -C "work@mail.com"` 針對工作帳號 work 重複上面的步驟，

###Step2

切換至 ssh 資料夾，並新增 config 檔案。

	$ cd ~/.ssh
	$ touch ~/.ssh/config

將下方 code 複製到 config 檔案中

	Host github.com-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_personal
    
    Host github.com-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_work
    
接下來將 project 的 remote url 設為 config 中的 host 名稱。

例如原本是 git@<font color="red">github.com</font>:personal/personalproject.git 更新為 git@<font color="red">github.com-personal</font>:personal/personalproject.git
 
	$ cd yourPersonalProjectPath
	$ git remote set-url origin git@github.com-personal:personal/personalproject
	$ cd yourWorkProjectPath
	$ git remote set-url origin git@github.com-work:work/workproject

若此時仍是以原先帳號上傳，則可使用指令 `ssh-add -l` 查看目前 ssh agent 有在管控的 key passphrase 。

使用 `ssh-add -d` 把所有舊的 ssh agent 管控的 key passphrase 都清掉。

也可使用 `ssh -T github.com-personal` 測試是否有已設定的 ssh 連線，

正確的話 username 會出現相應的名稱，例如 **personal** 。

Hi **username**! You've successfully authenticated, but GitHub does not provide shell access.

---

相關文章：

[Git: 批量變更已 commit 的創作者(author)及欲 commit 的創作者](http://summerlize.github.io/blog/2016/05/21/git-batch-change-author-of-previous-old-commits-and-new-commits)

