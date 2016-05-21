---
layout: post
title: "Grails: nullable 屬性導致 unique 限制式失效"
date: 2014-09-30 01:39
comments: true
categories: Grails 
---
Grails 版本: 2.3.11

當 domain 中 unique 條件關聯的屬性包含了 nullable 類型，

grails 會忽視 unique 限制導致誤判，

根據 api 可在 constraints 中加入 validator 來自定義需要驗證的內容。

假設 Domain 定義如下：

{% codeblock Book.groovy lang:groovy %}

class Book{

	String name
	String title  
	Author author
	Publisher publisher

	static constraints = {
		name(unique:['author','publisher'])
		publisher nullable:true   
	}
}

{% endcodeblock %}

由於 publisher 允許為 null，造成 `name(unique:['author','publisher']` 沒有正確判斷，

此時可以自行加入判斷如下：

{% codeblock lang:groovy start:8 %}
	static constraints = {
		name(unique:['author','publisher'],
			validator: { name, book ->
				def existingRecord = Book.withCriteria(){
        		
					if (book.id){
						ne('id', book.id)
					}
                    
					eq('author', book.author)
                    
					if (book.publisher){
						eq('publisher', book.publisher)
					}
					else {
						isNull('supplier')
					}
				}//end withCreiteria
			}//end validator
		)
		publisher nullable:true  
	}
}

{% endcodeblock %}

完成後就可以正確判斷 unique 摟～

