---
layout: post
title:  "sublime text3 snippet"
subtitle:   ""  
date:       2016-07-09
author:     "allanruin"
header-img: "img/posthead/stock-photo-71702253.jpg"
<!-- header-mask: 0.5 -->
catalog: false
tags:
    - sublime
    - python
---

# 自定义snippet

因为我记不住python源码开头的那个UTF-8到底怎么拼，所以每次都是打开onenote搜索utf8，然后copy语句到源码里。今天烦了，打算给sublime配置个自定义的补全脚本。结果也是小折腾了一下，疑问保存文件的时候并没有提示文件名后缀，我就自己随便编了个后缀。结果发觉一直不生效，找了下网上，这个[hongkiat](http://www.hongkiat.com/blog/sublime-code-snippets/)的网站好像挺不错，不是第一次进这个网站了。snippet文件后缀必须保存为`.sublime-snippet`才行。

``` xml
<snippet>
	<content><![CDATA[
# -- coding: UTF-8 
import sys
reload(sys)
sys.setdefaultencoding('utf-8')

]]></content>
	<!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
	<tabTrigger>utf8</tabTrigger>
	<!-- Optional: Set a scope to limit where the snippet will trigger -->
	<scope>source.python</scope>
	<description>让python默认使用UTF-8编码</description>
</snippet>
```