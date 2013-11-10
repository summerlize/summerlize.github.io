---
layout: post
title: "Octopress安裝+GitHub架設部落格筆記"
date: 2013-11-10 05:11
comments: true
categories: Octopress
---

終於在奮戰了幾天之後，架好了我的 Octopress 。 >__< 

還記得小時候 (?) 一直都有寫 blog 的習慣，反而因為變成大人 (?) 之後而荒廢了，

雖然號稱小小程式設計者，但我的學習歷程一直著重在邏輯的處理，

技術上長期沒什麼突破...(汗)

anyway 在某因緣際會下認識了 Markdown 進而發現了 Octopress 這樣的好東西，

不但可以用來撰寫技術文章，還可以達到快速排版的效果，

對於我這個懶惰蟲而言還蠻有吸引力。

可是～～～～～～～～～～～～～～～～～～～～～～～～～～

對於一個剛接觸「指令操作」不久、對 git 完全不熟悉的新手真的會覺得有點恐懼，

因此在這邊記錄我的安裝過程以及遇到的問題，希望能對跟我一樣困惑的小朋友有所幫助。:P

####作業系統：OS X 10.9

###Step1 安裝GIT
直接到網址下載安裝即可。

<http://code.google.com/p/git-osx-installer>

或者參考官網安裝方式。

<http://git-scm.com/book/zh-tw/%E9%96%8B%E5%A7%8B-%E5%AE%89%E8%A3%9DGit>

###Step2 安裝Ruby
Ruby有兩種版本控管的工具，分別為 RVM 及 Rbenv ，**選擇一種安裝即可**。

一開始我照著官網安裝 RVM 失敗了，所以當時是改以 Rbenv 來安裝。

但因為歷史久遠我已經忘記拉～所以這邊先依照官網的說明貼上ＸＤ

下面是針對 Mac 建議的安裝方法，先使用 Homebrew 套件管理工具來安裝 Rbenv 。

由於 Rbenv 只對 Ruby 進行版本控管，並沒有安裝的功能，必須另外安裝 ruby-build 才能進行安裝。

#####Rbenv

	$ brew update
	$ brew install rbenv
	$ brew install ruby-build

安裝完 Rbenv 及 ruby-build 後便可以開始安裝 ruby 囉！

最後必須執行 rehash 來進行版本重置。

	$ rbenv install 1.9.3-p0
	$ rbenv rehash
	
然後～～～～

我的第一次 Octopress 安裝就到這裡中斷了(掩面)，

當時假會的裝了新版的Ruby 2.0 ，

後來竟然和我的工作環境衝突，只好進行降版，

一開始使用 Rbenv 降版失敗幾次後，~~被迫改以 RVM 重新安裝~~，

~~畢竟工作時間就是金錢，老闆是不會等我 try N 次的(哭)~~，

一樣乖乖地輸入完下面的指令就可以摟～

#####RVM
	
	$ \curl -L https://get.rvm.io | bash
	$ \curl -L https://get.rvm.io | bash -s -- --autolibs=read-fail
	$ \curl -L https://get.rvm.io | bash -s stable --ruby
	
	$ rvm install 1.9.3p327

完成後可以輸入 `ruby --version` 來檢視是否安裝成功，

輸出訊息如： *ruby 1.9.3p327 (2012-11-10 revision 37606) [x86_64-darwin13.0.0]*。

準備好 Octopress 的前置工作終於可以進入正題了！

###Step3 安裝Octopress
先將 Octopress 的 source code 複製到本機，

指令的最後指的是你希望存放的資料夾名稱，如果參考官網預設是使用 `octopress` 。

	$ git clone git://github.com/imathis/octopress.git yourProjectName
	$ cd yourProjectName

接著安裝 bundler 。

	$ gem install bundler
	$ rbenv rehash    #使用Rbenv套件者才需執行
	$ bundle install

最後安裝 Octopress 預設的布景主題。

	$ rake install

基本上到這邊算是完成了 Octopress 的安裝，可以輸入以下指令來檢視是否安裝成功。

	$ rake generate
	$ rake preview

執行 `rake preview` 啓動 server 後，可以在 <http://localhost:4000> 檢視部落格。
		
###Step4 部署GitHub
首先必須申請 [GitHub](https://github.com/) 帳號，並且建立一個用來存放部落格代碼的程式庫 (Repository)。

Repo 的命名必須遵照規則 **username.github.io** ，

例如我帳號/組織代號為 **summerlize** ，Repo則需命名為 **summerlize**.github.io 。

輸入指令設定GitHub page。

	$ rake setup_github_pages

接著會要求輸入 Repo 的 URL，ex：*git@github.com:summerlize/summerlize.github.io.git* ，

由於 SSH 對大小寫檢查非常敏感，這邊建議從 GitHub Repo 頁面中右下方的 SSH clone URL 直接複製貼上。

![GitHub: SSH clone URL](../../../../images/blog/github_ssh.png)





