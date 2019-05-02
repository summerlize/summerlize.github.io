---
layout: post
title: "ExtJS 版本升級 從 5.1.1 到 6.7.0"
date: 2019-04-15 04:35
comments: true
categories: [ExtJS]
---

夏天搭配 ExtJS 使用的工具有 Sencha CMD 和 Sencha Architect，

因此升級時也是搭配工具處理，同時也會將工具版本更新，若沒有使用工具仍然可以手動作業替代，

以下是此次升級的內容：

- ExtJS 5.1.1 &rarr; 6.7.0

- Sencha CMD 6.1.2.15 &rarr; 6.7.0.63

- Sencha Architect 3.5.1 &rarr; 4.2.5

因為 Architect 無法一次升到最高版本，因此我這邊也會採取分次升級的方式進行，

且 Architect 3.5.1 的 ExtJS 最高版本為 6.0.x，

夏天選擇先不升級工具版本，將 ExtJS 升到當前最高後再升級工具。

### ExtJS 5.1.1 &rArr; 6.0.2

使用 CMD 指令進行升級，置換 project 中的 ext sdk 資料夾，

	$ sencha app upgrade path/to/ext60
	
將 project 中自行引入的 ux 或 plugin 移至 project/resources/js/ 底下，

因為使用 Architect 不可手動設定 app.json 中的 classpath，

將其放置於預設路徑底下便可正確導入，

接著使用 Architect 內的功能升版，此步驟只是讓 Architect 識別使用的 ExtJS 版本，

	Project Setting > Framework > upgrade to EXT JS 6.0.x

由於新版將 ext- 或 sencha- 開頭的套件 prefix 去除，

因此我們將 app.json 中 的 theme 由 "ext-theme-neptune" 改為 "theme-neptune"。

其他：

1. 新版編譯檔多了副檔名為 jsonp 的檔案，將其加入 gitignore 中。
2. 將專案中引用的開源套件，更換升級至相應版本。
3. 新的 extjs 將 css build 成兩個檔案，
   
   但因爲我們的專案有置換 css 的讀取順序，
   
   所以需要編譯成一個檔案，參考 [ExtJS 6 css 設定編譯成單一檔案](/blog/2019/04/16/extjs-build-without-split-all-css-or-with-single-css)。


### CMD 6.1.2.15 &rArr; 6.7.0.63

安裝 Sencha CMD 6.7.0.63 後，與專案中使用指令升級：

	$ senhca app upgrade

### Architect 3.5.1 &rArr; 4.2.5

安裝 Sencha Architect 4.2.5 後，使用 Architect 開啟專案並儲存即可。

### ExtJS 6.0.2 &rArr; 6.2.1

使用 CMD 指令進行升級，置換 project 中的 ext sdk 資料夾，

	$ sencha app upgrade path/to/ext62

接著使用 Architect 內的功能升版，此步驟只是讓 Architect 識別使用的 ExtJS 版本，

	Project Setting > Framework > upgrade to EXT JS 6.2.x Classic	
此時 Architect 會將過去建立的 override js 加入 app.json之中，

必須手動將這些 js 的 config requiresSdk 設置為 true 才能正確編譯。

其他：

1. 將專案中引用的開源套件，更換升級至相應版本。 
2. 專案中有 accordion layout 的 container，
   
	其中 placeholder config 使用 object 無法被正確 create，
   
	改成在 event render 時使用 Ext.create 建立後再設定 placeholder。

### ExtJS 6.2.1 &rArr; 6.5.3 &rArr; 6.6.0 &rArr; 6.7.0

接下來的升級都是重複動作，比較沒有需要額外調整的內容，只要將 sdk 指定至正確版本即可。

使用 CMD 指令進行升級，置換 project 中的 ext sdk 資料夾，

	$ sencha app upgrade -full path/to/ext65

接著使用 Architect 內的功能升版，此步驟只是讓 Architect 識別使用的 ExtJS 版本，

	Project Setting > Framework > upgrade to EXT JS 6.5.x Classic


