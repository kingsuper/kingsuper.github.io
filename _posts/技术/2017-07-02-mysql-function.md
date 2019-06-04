---
layout: post
title: MySQL 学习笔记-函数
description: MySQL 学习笔记-函数
category: 技术
---

PS: 学习主要参考来自《mysql必知必会》，如有需要，请购买正版图书，谢谢。

## 1.Concat 拼接多个字段

    SELECT Concat(prod_name,'_',prod_price) as concat_title FROM products ;

## 2.RTrim 去掉字段右侧的空格

    SELECT RTrim(prod_name) AS trim_prod_name FROM products ;

## 3.AVG 平均值(MAX MIN SUM 函数类似)

AVG函数，会计算所有符合条件的记录，非NULL的该列数值的平均值。

    SELECT AVG(prod_price) as avg_price FROM products;

## 4.COUNT 计数

    SELECT COUNT(*) AS sum_record FROM products;

* COUNT(\*) 无论记录是否为空，都计算为一行
* COUNT(column_name) column_name列不为NULL的行数
