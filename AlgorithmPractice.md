---
title: AlgorithmPractice
typora-copy-images-to: AlgorithmPractice
toc: true
date: 2021-11-17 21:07:54
tags:
index_img:
categories:
---

- 想要在一个有序范围内查找首个大于目标值的数，可以在循环内将找到的大于目标值的数赋值给ans，然后缩小二分查找范围到`[left, ans-1]`
