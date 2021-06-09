---
layout: post
title: Python爬虫05-验证码识别 & coockie & 代理ip
date: 2021-02-19 10:16:06
tags:
categories: Python爬虫
---
爬虫中的验证码自动识别、coockie、代理ip。
<!--more-->
- 验证码：一种反爬机制
  - 识别验证码：第三方自动识别（超级鹰）
- cookie：让服务器端记录客户端的相关状态（是否登录等）
  - 手动处理：使用抓包工具找到coockie值，复制封装到headers中
  - 自动处理：使用session会话对象
    - 作用：
        1. 进行请求的发送
        2. 将请求过程中产生的coockie自动存储/携带在session对象中
    - 步骤：
        1. 创建一个session对象：``session = requests.Session()``
        2. 使用session对象发送用于模拟登录的post请求
        3. 使用session对象对页面进行（携带了coockie）的get请求
- 代理ip：使用代理服务器，破解一段时间内，网站禁止同一IP访问次数过多的反爬机制。
  - 作用：
    1. 突破自身IP访问的限制
    2. 隐藏自身真实IP
  - 代理相关网站：
    1. 快代理
    2. 西祠代理
    3. www.goubanjia.com
  - 代理ip的类型：
    1. http：应用到http协议对应的url中  
      ``requests.get(url = url, headers = headers, proxies = {"https":"222.110.144"})``
    2. https：应用到https协议对应的url中
  - 代理ip的匿名度：
    1. 透明：服务器知道使用了代理ip，且知道真实ip
    2. 匿名：服务器知道使用了代理ip，但不知道真实ip
    3. 高匿：服务器不知道使用了代理ip


