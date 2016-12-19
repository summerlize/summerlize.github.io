---
layout: post
title: "Grails: 呼叫/合併兩個以上 criteria 閉包"
date: 2016-12-08 20:00
comments: true
categories: Grails
---
Grails 版本: 2.4.5

### 方法一：委派閉包執行對象

參考 [官方 references #combining criteria](http://docs.grails.org/2.4.5/guide/single.html#criteria)

{% codeblock Grails - combining criteria closures lang:groovy %}

class CriteriaFilters {
	def create = {
		def condition = {
			eq("title", "Harry Potter")
		}
	}
}


class BookController {
	def index() {
		def lastUpdated = Book.createCriteria().get {
			projections{
				max 'lastUpdated'
			}
		}
		
		def condition = new CriteriaFilters().create()
		
		Book.createCriteria().list(max: 10, offset: 10) {
			condition.delegate = delegate
			condition()
			//可加入其他規則
			eq('lastUpdated', lastUpdated)
		}
	}
}

{% endcodeblock %}

delegate 預設儲存為自身 owner，表示由 owner 來執行，print 出來會比較好理解， 

沒有置換 condition.delegate 的列印結果為 ``CriteriaFilters$_closure1_closure2@72fb2b75`` ，

執行 index() 會產生找不到 eq method 的錯誤訊息 ``No signature of method: CriteriaFilters.eq() `` ，

因為 CriteriaFilters 中不存在 eq 函式。

置換 condition.delegate 後的列印結果為 ``grails.orm.HibernateCriteriaBuilder@2e0f446c`` ，

此時會正確指派 HibernateCriteriaBuilder 類別來執行，而傳送給 createCriteria 的 closure 則可繼續加入其他規則。


### 方法二：於 domain 中預設 query criteria

參考 [官方 API namedQueries 範例](http://docs.grails.org/2.4.5/ref/Domain%20Classes/namedQueries.html)

使用 domain 的 namedQueries 屬性來自訂 criteria 方法，

直接使用 domain 呼叫自訂的查詢方法 取代 createCriteria/withCriteria 即可，

再於最後加入包含其他規則的第二個閉包。

{% codeblock Grails - namedQueries example from official API lang:groovy %}

class Publication {
   String title
   String author
   Date datePublished
   Integer numberOfPages

   static namedQueries = {
       recentPublications {
           def now = new Date()
           gt 'datePublished', now - 365
       }

       oldPublicationsLargerThan { pageCount ->
           def now = new Date()
           lt 'datePublished', now - 365
           gt 'numberOfPages', pageCount
       }

       publicationsWithBookInTitle {
           like 'title', '%Book%'
       }

       recentPublicationsWithBookInTitle {
           // calls to other named queries…
           recentPublications()
           publicationsWithBookInTitle()
      }
   }
}


def books = Publication.recentPublications.list(max: 10, offset: 5) {
	//可加入其他規則
    or {
        like 'author', 'Tony%'
        like 'author', 'Phil%'
    }
}

{% endcodeblock %}

### 方法三：將 HibernateCriteriaBuild 設為參數，使其他方法可用以執行規則

與方法一概念類似，但沒有傳送閉包，

而是將 delegate 當成引數傳送給自訂的方法，由方法一已知 delegate 為 HibernateCriteriaBuild 類別，

於自訂的方法呼叫 HibernateCriteriaBuild 類別執行規則，兩種做法都可以視需求使用，

但第一種會比較彈性，當只需要一個 criteria 閉包時，不需指定 delegate 就可以直接使用。 

而此方法則是每次都必須傳送 delegate。

{% codeblock book update - solution 2 lang:groovy %}

Book.createCriteria().list(max: 10, offset: 10) {
    title(delegate, 'Harry Potter%')
    author(delegate, 'J. K. Rowling')
    //可加入其他規則
}
 
private void title(builder, String title) {
    builder.ilike 'title', title
}
 
private void author(builder, String authorName) {
    builder.eq 'author', authorName
}

{% endcodeblock %}


<font color="#fcfcfc">gorm criteria、條件、規則、sql</font>
