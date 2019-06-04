---
layout: post
title: MySQL学习笔记-创建和操作表
description: MySQL学习笔记-创建和操作表
category: 技术
---

## 1.CREATE TABLE 创建表
    CREATE TABLE　customers
	(
	  cust_id int NOT NULL AUTO_INCREMENT,
	  cust_name char(50) NOT NULL,
	  cust_address char(50) DEFAULT "ads",
	  ...
	  PRIMARY KEY(cust_id)
	) ENGINE=InnoDB;
	
	
NOT NULL 指定该列不能为空，
DEFAULT 指定该列默认值为ads，如果插入时不指定该列，则默认为此值
AUTO_INCREMENT 自增
ENGINE 指定引擎，MySQL 通常有三种引擎，InnoDB支持事务，不支持全文本搜索，MEMORY
功能上等同MyISAM,数据存储在内存中，速度快，MyISAM支持全文本搜索，但不支持事务处理。

## 2. ALTER　TABLE 更新表

### 2.1 添加列
    ALTER TABLE vendors ADD vend_phone CHAR(20);
    
### 2.2 删除列
    ALTER TABLE vendors DROP COLUMN vend_phone;
    
### 2.3 增加外键
    ALTER TABLE orderitems ADD CONSTRAINT fk_orderitems_orders 
    FOREIGN KEY (order_num) REFERENCES orders (order_num);
    
### 2.4 复杂的表结构修改
* 用新的列创建1个新表
* 使用INSERT　SELECT 语句复制数据从旧表到新表
* 检验包含所需数据的新表
* 重命名旧表
* 用旧表的原名称重命名新表
* 根据需要，重新创建触发器、存储过程、索引和外键

## 3.DROP TABLE　删除表
    DROP TABLE customers;
    
## 4.RENAME TABLE 重命名表
    RENAME TABLE customers2 to customers;
