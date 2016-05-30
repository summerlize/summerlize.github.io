---
layout: post
title: "Grails: GORM 自動更新儲存問題"
date: 2014-05-14 03:59
comments: true
categories: Grails
---
Grails 版本: 2.3.5

以下面的程式碼為例，

我們希望找到 author 之後，將 author 存回 book 中，

再呼叫 book 做 save()，

但實測發現 book 在 if 之前就已自動儲存，

{% codeblock book update lang:groovy %}

def book = Book.get(params.id)
book.properties = params

def author = Author.findByName(params["author.name"])

if(author){
	book.author = author
	book.save()
}

{% endcodeblock %}

查了一下 Grails API 找到 [discard()](http://grails.org/doc/2.3.4/ref/Domain%20Classes/discard.html) 方法，**用來避免自動執行儲存**，

測試的程式碼如下，結果 book 仍然自動更新 (暈) ，

{% codeblock book update - test discard lang:groovy %}

def book = Book.get(params.id)
book.properties = params

def author = Author.findByName(params["author.name"])

book.discard()

{% endcodeblock %}

繼續 try 不同的組合後發現似乎是** findBy() 指令造成 book 自動儲存**，

在此提供兩種解決方式，**第一種是在 mapping params 之前先執行完 findBy() **，

{% codeblock book update - solution 1 lang:groovy %}

def author = Author.findByName(params["author.name"])

def book = Book.get(params.id)
book.properties = params

if(author){
	book.author = author
	book.save()
}

{% endcodeblock %}

若因為處理邏輯的因素 findBy() 一定得在 book.properties = params 之後，

我們可以用**第二種方式，使用 new Book(params) 來取代 book.properties = params **。

{% codeblock book update - solution 2 lang:groovy %}

def book = new Book(params)

def author = Author.findByName(params["author.name"])

if(author){
	def book = Book.get(params.id)
	book.properties = params
	book.author = author
	book.save()
}

{% endcodeblock %}
