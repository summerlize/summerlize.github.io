---
layout: post
title: "Grails: 將 resources plugin 替換為 asset-pipeline"
date: 2016-05-22 02:16
comments: true
categories: [Grails]
---
根據 Grails 官方 plugins 的介紹， Asset-Pipeline 是用來管理與處理靜態資源，包含壓縮及打包 CSS 與 JS 檔，且支援編譯自訂的靜態語言，例如：CoffeeScript 。

Asset-Pipeline 是用來取代相同作用的 resources-plugin ，並提供更有效益及友善的開發架構。透過最小化 JS 來降低靜態資源的大小。與 resources plugin 的差異如下：

1. 動態處理，不需要因為檔案變更而重新載入資源。
1. 在 war 產生時編譯資源，不影響到 server 啟動的時間。
1. 經由壓縮、最小化、建立緩存減少檔案間的交互作用。
1. 在 development 模式中，保持各個檔案獨立 (不壓縮成一個檔案)，方便除錯。
1. 使用簡易的 manifest 和 taglib 設計 (清單與標籤) 來讀取資訊。

接下來就要將現有專案中的 resources plugin 更換為 asset-pipeline 囉～

###step 1:

將*yourProjectPath*/grails-app/conf/**buildConfig.groovy** 中 ``runtime ':resources:1.n.n'`` 替換為 ``org.grails.plugins:asset-pipeline:2.n.n``

{% codeblock grails-app/conf/buildConfig.groovy lang:groovy %}

plugins {
        build ':tomcat:7.0.54'

        runtime ':hibernate4:4.3.5.2'
        runtime ':database-migration:1.4.0'
        compile ":jquery:1.11.1"
        
        runtime ':resources:1.2.14' //刪除此行
        compile "org.grails.plugins:asset-pipeline:2.8.0" //新增此行
 }

{% endcodeblock %}

啟動 grails 會發現 *yourProjectPath*/grails-app 底下多了 **assets** 資料夾

###step 2:

1. 將 *yourProjectPath*/web-app/**js** 中的檔案複製至 *yourProjectPath*/grails-app/assets/**javascripts**
1. 將 *yourProjectPath*/web-app/**css** 中的檔案複製至 *yourProjectPath*/grails-app/assets/**stylesheets**
1. 將 *yourProjectPath*/web-app/**images** 中的檔案複製至 *yourProjectPath*/grails-app/assets/**images**

   ps. 如沒有其他程式碼使用 web-app 中三個資源資料夾的內容，可自行決定是否將其刪除。

1. 刪除 resources plugin 相關的 config 設定，搜尋 project 中 **grails.resources** 開頭的 config。

   ex: grails.resources.debug=true

   ex: grails.resources.adhoc.patterns = ['/images/*', '/css/*', '/js/*', '/plugins/*']

###step 3:

1.  參考原先 GSP 頁面所載入的 js 、 css ，
	
	在 *yourProjectPath*/grails-app/assets/**javascripts** 中新增一隻 js 檔，用來編輯 GSP 檔所需載入的 js。

	在 *yourProjectPath*/grails-app/assets/**stylesheets** 中新增一隻 css 檔，用來編輯 GSP 檔所需載入的 css。
	
	*yourProjectPath*/grails-app/**views/myGspPage.gsp**
		
		<r:require modules="js, css"/> // 載入 js 、 css module

	*yourProjectPath*/grails-app/**conf/ApplicationResources.groovy**
		
		modules = {
			js { // js module
			    resource url: 'js/bootstrap.js'
			    resource url: 'js/magnific-popup.js'
			    resource url: 'js/app/models.js'
			}
			css { // css module
			    resource url: 'css/bootstrap.css'
			    resource url: 'css/magnific-popup.css'
			    resource url: 'css/layout.css'
			    resource url: 'css/style.css'
			}
		}

	在新的 js 檔中編寫要載入的 js 檔，以 `//=` 開頭。

	*yourProjectPath*/grails-app/assets/**stylesheets/application.js**
		
		//= require app/models.js
		//= require bootstrap
		//= require magnific-popup

	在新的 css 檔中編寫要載入的 css 檔，以 `*=` 開頭。
	
	*yourProjectPath*/grails-app/assets/**stylesheets/application.css**
	
		/*
		*= require bootstrap
		*= require magnific-popup
		*= require layout
		*= require style
		*/



1.  將 GSP 頁面載入 js、css 的程式碼改為 asset-pipeline寫法。

	*yourProjectPath*/grails-app/**views/myGspPage.gsp**
		
		<r:require modules="js, css"/>              // 改寫此行
		
		<head>
		<asset:javascript src="application.js"/>    // 加入剛才新增的 js 載入清單
		<asset:stylesheet href="application.css"/>  // 加入剛才新增的 css 載入清單
		</head>

1.  將 GSP 頁面載入圖片的程式碼改為 asset-pipeline寫法。

	*yourProjectPath*/grails-app/views/**myGspPage.gsp**
	
		// 改寫以下兩行
		<r:img uri="images/logo.png" width="100" height="50"/>
		<r:img dir="images" file="logo.png" width="100" height="50"/>
		
		// asset-pipeline 載入圖片寫法
		<head>
		<asset:link rel="shortcut icon" href="favicon.ico" type="image/x-icon"/>
		<asset:link rel="shortcut icon" href="logo.png" width="100" height="50"/>
		</head>
		
1.  將 GSP 頁面載入資源的程式碼改為 asset-pipeline寫法。

	*yourProjectPath*/grails-app/views/**myGspPage.gsp**

		//改寫此類語法
		<link rel="shortcut icon" href="${resource(dir: 'images', file: 'favicon.ico')}" type="image/x-icon" />
		
		//asset-pipeline 寫法
		<link rel="shortcut icon" href="${assetPath(src: 'images/favicon.ico')}" type="image/x-icon" />

1.  將 GSP 頁面所有 <r:script> 標籤改為 <asset:script> 、 </r:script> 標籤改為 </asset:script> 。
	
	並在主要的 GSP 頁面 body 區塊加入 `<asset:deferredScripts/>` ，用來指定輸出 <asset:script> 的位置，一般在段落的最末處。

1.  將 GSP 頁面所有 <g:javascripts 標籤改為 <asset:script。

1.  刪除 resources plugin 資源模組定義檔案 *yourProjectPath*/grails-app/conf/**ApplicationResources.groovy**

1.  刪除 GSP 頁面中所有 `<r:layoutResources/>` 標籤。

1.  確認所有 GSP 頁面中沒有 `<r:` 開頭的標籤

	




