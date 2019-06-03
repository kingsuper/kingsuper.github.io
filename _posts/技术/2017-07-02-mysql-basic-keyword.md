---
layout: post
title: MySQL 学习笔记-基本关键字
description: MySQL 学习笔记-基本关键字
category: 技术
---
PS: 学习主要参考来自《mysql必知必会》，如有需要，请购买正版图书，谢谢。

<h2>1.操作数据库</h2>

```
use database_name; //连接数据库
show databases; //显示数据库列表
show tables; //显示数据库表
```

<h2>2. DISTINCT</h2>

返回不同的值，查询指定DISTINCT关键字，则返回的行，只显示不同的内容。需要注意，该关键字必须直接放在列名的前面。示例：

```
SELECT DISTINCT vend_id FROM products;
```

<h2>3. LIMIT</h2>

只返回指定行的记录，也可以分页返回。例如 LIMIT 5显示5行，而LIMIT 5，5显示为从第五行开始的五行。示例：

```
SELECT prod_name FROM products LIMIT 5,5
```

<h2>4. 排序</h2>

```
ORDER BY column //排序按照column
ORDER BY column DESC //降序排列，系统默认为升序排列，升序关键词为ASC
```

<h2>5.&#95;通配符</h2>

下划线通配符只匹配单个字符，%通配符匹配多个字符

```
SELECT * FROM products WHERE prod_name LIKE '_ ton anvil';
仅匹配1 ton anvil 2 ton anvil，不匹配.5 ton anvil
```

<h2>6. 匹配正则表达式REGEXP</h2>

和Like一样使用使用，只不过REGEXP后面跟的是正则表达式

```
    SELECT * FROM products WHERE prod_name REGEXP '[1-5] Ton' 返回所有prod_name中包含1-5 Ton的记录
```

<h2>7.空值检查 IS NULL</h2>

主要用来判断某列是否为空：

```
SELECT prod_name FROM products WHERE prod_price IS　NULL;
查询产品价格不为空的所有产品。
```

<h2>8.GROUP BY 分组</h2>

<ul>
<li>GROUP BY 可以包含任意数目的列。</li>
<li>如果分组列中有NULL值，则NULL将作为一个分组返回。</li>
<li>GROUP BY 必须放在WHERE后，ORDER BY 前。 </li>
</ul>

<h2>9. HAVING 过滤分组</h2>

使用方法和WHERE相同，只不过是用来过滤分组。

```
GROUP BY vend_id HAVING count(*) &gt;=2 ;
首先过滤价格大于10的记录，然后分组，然后过滤掉分组后个数大于等于2的产品。
```

<h2>10. 查询语句SELECT顺序</h2>

```
SELECT－FROM -WHERE-GROUP BY-HAVING-ORDER BY-LIMIT
```
