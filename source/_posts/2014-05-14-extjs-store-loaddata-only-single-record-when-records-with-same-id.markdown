---
layout: post
title: "Ext JS: store loadData() id 重複導致只顯示一筆資料"
date: 2014-05-14 11:55
comments: true
categories: ExtJS
---
Ext JS 版本: 4.2.x

Sencha Architect 版本: 2.2.3

此處希望建立一個共用的 grid 來顯示不同 table 的資料，

如下 Json Data 所示：


	Json Data
	[{"id":1, "name": "Java Programming", "title": "Java 程式設計"},
	 {"id":1, "name": "Fashion Magazine", "title": "時尚雜誌"}]

在執行 ``grid.getStore.loadData(record)`` 後卻只有顯示一筆資料，

檢查後發現是 id 重複造成，

由於資料存於不同的資料表，因此後端 query 出預備載入的 record 便可能會有重複 id ，

API 說明 proxy 中 reader 的 idProperty 設定，是用該欄位作為 record 的唯一識別值，預設為 id 欄位，

因此我們可以將 idProperty 定義為實際的唯一識別欄位，避免相同 id 的資料被覆蓋，

由於我的 record 並沒有唯一識別值，暫時設置為 undefined。


{% codeblock book update lang:groovy %}

proxy: {
	type: 'memory',
	reader: {
		type: 'json',
		idProperty: 'undefined'
	}
}
    
{% endcodeblock %}

另外 API 也提到 ``store.loadData(record)`` 定義為預設資料已經處理成正確格式，

因此不會經過 reader 二次處理，

若希望使用 reader 處理，需改為使用 ``store.loadRawData(record)``。


