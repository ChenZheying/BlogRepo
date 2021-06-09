---
layout: post
title: Python学习笔记01-简单数据类型
date: 2021-04-30 07:57:26
tags: [编程语言]
toc: true
categories: Python学习
---
|语法|作用|示例|
|:-:|:-:|:-:|
|title()|获得字符串将每个单词的**仅**首字母大写的字符串|`full_name.title()` 'adA loveLace' -> 'Ada Lovelace'|
|rstrip()|获得删除字符串末尾的空白之后的字符串|`favorite_language.rstrip()` 'python ' -> 'python'|
|lstrip()|获得删除字符串开头的空白之后的字符串|`favorite_language.rstrip()` ' python' -> 'python'|
|strip()|获得删除字符串开头和末尾的空白之后的字符串|`favorite_language.rstrip()` ' python ' -> 'python'|
|**|乘方|`3 ** 2 `= 9|
|str()|强制转换为string类型|`age = 23; str(age)`|