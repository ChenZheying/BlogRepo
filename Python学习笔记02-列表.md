---
layout: post
title: Python学习笔记02-列表
date: 2021-05-02 06:49:56
tags: [编程语言]
toc: true
index_img: /img/python.jpeg
categories: Python学习
---

<escape><!-- more --></escape>

- 列表是有序集合（可随机访问）。
- 索引为-1，表示列表的最后一个元素。
- `some_list.append(some_value)`：在列表末尾添加元素。
- `some_list.insert(index, some_value)`：在列表的索引位置插入新元素。
- `del some_list(index)`：删除列表索引位置的元素。
- `some_value = some_list.pop()`：列表“出栈”。
- `some_value = some_list.pop(index)`：列表指定索引位置元素“出栈”。
- `some_list.remove(some_value)`：删除列表中值等于指定值的第一个元素。
