---
layout: post
title: MySQL学习笔记-数据新增、更新和删除
description: MySQL学习笔记-数据新增、更新和删除
category: 技术
---
## 1.INSERT 插入数据
    INSERT INTO Customers VALUES(NULL,'a','b','c','d'...)
	这种语法比较简单，但比较危险，应该避免使用。
	更安全的方法如下：
	INSERT INTO customers(cust_name,cust_address,...) VALUES('a',NULL,...)

## 2. 根据检索出的数据插入
   可以利用SELECT查询的结果插入到对应的表中，示例如下：
    
	INSERT INTO customers(cust_id,cust_contact,...)
	SELECT(cust_id,cust_contact,...) FROM custnew;

## 3. UPDATE 更新数据
    UPDATE customers SET cust_name='a',cust_email='b' WHERE cust_id= 10005;
如果更新多行时出现一个错误，则整个更新会回退，如果发生错误，仍然继续更新，则需要增加关键字IGNORE ： UPDATE IGNORE customers...

    UPDATE customers SET cust_email = NULL WHERE cust_id=10005
	可以删除某个列的值，置为NULL。

## 4. DELETE 删除数据
    DELETE FROM customers WHERE cust_id = 10006;
	
## 5. UPDATE 和DELETE 的指导原则:
* 除非打算更新和删除每一行，则不要使用不带WHERE的UPDATE和DELETE
* 保证每个表都有主键
* 在进行UPDATE和DELETE前，先使用SELECT进行测试，保证过滤的是正确的记录
* 使用强制实施引用完整性的数据库
