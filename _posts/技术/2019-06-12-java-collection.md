---
layout: post
title: Java 编程思想学习笔记-容器
category: 技术
---

## 11.3 添加一组元素

Arrays和Collections都有一些方法，可以在一个Collections里增加一组元素。

* Collections.addAll() 接受一个Collection对象，数组或者逗号分隔的元素列表 
* Arrays.asList() 接受一个数组或者逗号分隔的元素列表

Collection的构造器也可以接受另一个Collection来完成自身的初始化,但是Collection.addAll()方法会快很多。
Collection.addAll()只能接受一个Collection对象，所以不如Collections.addAll()和Arrays.asList灵活。这两个方法是可变参数列表。
可以使用ArrayList.asList()的输出，将其作为List，但是底层表示依旧是数组，不能调整尺寸。

## 11.4 容器的打印

ArrayList和LinkedList都是List类型，从输出可以看出，它们都按照插入的顺序保存元素。不同之处在于执行某些类型操作时的性能。LinkedList包含的操作也多于ArrayList。

HashSet、TreeSet、LinkedHashSet都是Set类型，每个相同的项只保存一次。HashSet是最快的获取元素的方式，因此存储的顺序并无实际意义。如果存储顺序很重要，可以使用TreeSet，它按照比较结果的升序保存对象。LinkedHashSet按照被添加的顺序保存对象。

Map不用考虑尺寸，它会自动的调整尺寸。与HashSet一样，HashMap也提供了最快的查找技术，也没有按照任何明显的顺序来保存元素。TreeMap按照比较结果的升序保存键，而LinkedHashMap按照插入顺序保存键，同时还保留了HashMap的查询速度。

## 11.5 List

有两种类型的List:

* 基本的ArrayList，长于随机访问元素，但是在List的中间插入和移除元素时较慢。
* LinkedList，插入和删除的代价较低，提供了优化的顺序访问。但是在随机访问方面相对较慢，但是特性集比ArrayList更大。

## 11.6 迭代器

* 迭代器设计模式

Java的Iterator只能单向移动，用来：

1. 使用iterator()的要求返回一个Iterator。
2. 使用next()获得序列中的下一个元素。
3. 使用hasNext检查序列中是否还有元素。
4. 使用remove将迭代器新返回的元素删除。

### 16.6.1 ListIterator

ListIterator是Iterator的子类型，但是更强大，只能用于List的访问，但是支持双向移动。还可以产生指向当前位置的前一个和后一个元素的索引。还可以使用set()方法替换它访问过的最后一个元素。
可以通过listIterator获取指向开始处的ListIterator，也支持通过listIterator(n)创建一个一开始就指向n的ListIterator.

## 11.8 栈

栈通常是指后进先出。LinkedList具有能够直接实现栈所有个功能的方法。可以进行封装，尽量不要使用java.util.Stack中的Stack.
