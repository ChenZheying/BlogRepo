---
title: C++Primer-03-const限定符
toc: true
date: 2021-07-29 15:11:30
tags:
index_img:
categories:
---
## const基础知识
- const限定符用于定义**值不能改变**的变量——常量。
- const对象创建之后值就不能改变，所以必须初始化。
- 根据所赋初值方式的不同，const对象有两种初始化：编译时初始化和运行时初始化
  - 编译时初始化：`const int j = 42;`
  - 运行时初始化：`const int i = get_size()`
