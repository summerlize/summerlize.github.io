---
layout: post
title: "使用Octopress部落格發佈文章"
date: 2013-11-12 02:38
comments: true
categories: Octopress
---

##1. Octopress Blog 基本設定

在 Octopress 資料夾中可以找到一個 `_config.yml` 的檔案，

是用來設定部落格產生時讀取的預設資料，可以分為以下幾個部分：

	基本預設資料：	
	url: http://summerlize.github.io            #部落格網址
	title: 這個夏天。	                           #部落格標題
	subtitle: 小小筆記本                          #部落格副標題
	author: SUMMERLIZE                          #作者名稱
	simple_search: http://google.com/search     #搜尋引擎
	description: 我是一個部落格                   #部落格描述
	date_format: "ordinal"                      #日期格式*
	subscribe_rss: /atom.xml                    #RSS服務
	subscribe_email: summerlize@gmail.com       #Email
	category_feeds: false                       #訂閱指定分類文章 預設為否
	email: summerlize@gmail.com	                #在RSS中列出Email 	
* 日期格式可到 [Ruby API](http://www.ruby-doc.org/core-2.0.0/Time.html) 查詢

有做過功課大概都會發現 Octopress 是利用 Jekyll 產生出靜態網頁的，

如果沒有特殊需求，大部份採用預設值即可。

	Jekyll靜態網頁產生器與附加元件設定：
	
	#直接部署的octopress專案通常不需要修改root，除非有另外包資料夾，
	#最簡單的方式是觀察網址，假如首頁為http://summerlize.github.io/project，那麼root便是/project
	
	root: /
	
	#此處為部落格文章的預設路徑，預設為/blog/年/月/日/文章標題
	
	permalink: /blog/:year/:month/:day/:title/
	
	#原始碼資料路徑，預設為source，同上面的例子則為/project/source
	
	source: source
	
	#預設編譯的資料夾路徑，預設為public，同上面的例子則為/project/public
	
	destination: public
	
	#預設附加元件路徑，預設為plugins，同上面的例子則為/project/plugins
	
	plugins: plugins
	
	code_dir: downloads/code
	
	#編譯後的部落格分類路徑
	category_dir: blog/categories
	

	markdown: rdiscount
	rdiscount:
  		extensions:
    		- autolink
    		- footnotes
    		- smart
    		
	pygments: false # default python pygments have been replaced by pygments.rb

	paginate: 10          #首頁顯示的文章數
	
	pagination_dir: blog  #Directory base for pagination URLs eg. /blog/page/2/
	
	recent_posts: 5       #最近發表的文章顯示篇數
	
	excerpt_link: "Read on &rarr;"  #文章末處提示繼續閱讀全文連結字樣
	
	titlecase: true       # Converts page and post titles to titlecase

	#側邊列表顯示的內容，預設為最近發表文章、github、delicious、pinboard、googleplus等。
	
	default_asides: [asides/recent_posts.html, asides/github.html, asides/delicious.html, asides/pinboard.html, asides/googleplus.html]

###按讚或分享部落格
官方內建的第三方服務包含了幾個知名社群類型網站，像是 Twitter 、Google+ 、Facebook 等，

另外也有 Google 統計分析工具之類的擴充。

	第三方服務設定（社群服務等）：
	github_user:					#輸入GitHub帳號便會自動生成按鈕連結，其他以此類推。
	github_repo_count: 0			#輸入GitHub Repo數
	github_show_profile_link: true #顯示GitHub檔案連結，預設開啟。

###COMMENTS功能
其中 disqus 的設定，是用來提供訪客留言的功能，大推～

必須先到 [Disqus官方網站](https://disqus.com/) 申請帳號後設定即可。

##2. Octopress Blog 新增/發佈文章

終於進入正題囉～首先使用 `rake -T` 可以查看 Octopress 的所有指令，

新增文章使用 `rake new_post["title"]`，在 title 處輸入文章標題即可，

有趣的是如果輸入的是中文，Octopress 會自動轉換成羅馬拼音的檔名，

因此建議還是輸入有意義的英文標題，撰寫文章時再改成中文標題摟～

產生的文章會在 octopress/source/_post/ 底下，

找到剛才新增的檔案用文字編輯器打開後，

便可使用 Markdown 編輯文章了（[下載好用的 Markdown 編輯器 Mou](http://mouapp.com/) ）。

完成文章之後，以 `rake generate` 產生靜態部落格網頁，

再以 `rake deploy` 發佈就可以到你的網域檢視了。

ps. 以我使用 GitHub 的經驗，第一次會很快，第二次之後大概需要 10 到 20 分鐘不等才會更新。

在發佈新文章之前，也可以使用 `rake preview` 來預覽你的部落格，

預設的預覽網址為 <http://localhost:4000/> 。

另外若要以 utf8 編碼在 generate 前需要設定，目前還沒有用過，記下來參考。

	set LC_ALL=zh_TW.UTF-8
	set LANG=zh_TW.UTF-8
