---
layout: post
title: MySQL 学习笔记-关联查询
description: MySQL 学习笔记-关联查询
category: 技术
---

PS: 学习主要参考来自《mysql必知必会》，如有需要，请购买正版图书，谢谢。

## 1.子查询

### 1.1 使用子查询作为Where的条件嵌套

    select * from orders where order_num in
    ( select order_num from orderitems where prod_id = 'TNT2');

### 1.2 使用外层查询的记录，执行子查询进行统计

    select  cust_name, cust_state,
    (select COUNT(*) from orders 
    where orders.cust_id = customers.cust_id) as orders 
    from customers ORDER BY cust_name;
    首先 执行外层查询，获取一条条的customer，然后
    通过customer的ID ，在orders表中，计算符合条件的个数。

## 2.联结

### 2.1 联结表

   不使用where语句的联结，实际上会产生所有笛卡尔集，集两个表行数的积。
   联结的基本语法如下：

    select * from vendors,products
    where vendors.vend_id=products.vend_id;

### 2.2 内部联结

   内部联结实际上就是普通的等值联结，和where产生的效果相同。

    select * from vendors INNER JOIN products
    on vendors.vend_id=products.vend_id;

## 3.高级联结

### 3.1 自然联结

   自然联结指的是每个列只返回1次，通常来说，这个需要自己控制。

    select p1.* from products as p1 , products as p2
    where  p1.vend_id=p2.vend_id and p2.prod_id='DTNTR'

### 3.2 外部联结

   外部联结指的是搜索出没有关联上的行。通常有两种，左联结和右联结。这里的左右
   指的是保留OUTER JION哪边的行。

    select * from customers LEFT OUTER join
    orders on customers.cust_id = orders.cust_id;

## 4. 组合查询 UNION

   UNION就是将两条查询语句的结果合并返回。有几条规则需要注意：

    select * from products where prod_price <=5 UNION
    select * from products where vend_id in (1001,1002);

* UNION中的每个查询必须包含相同的列、表达式或聚集函数
* 列数据类型必须兼容
  
### 4.1 不去除重复的行 UNION ALL

     select * from products where prod_price <=5 UNION ALL
     select * from products where vend_id in (1001,1002);
