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

## 11.9 Set

Set不保存重复的元素，最常使用的是测试归属性，可以很容易的询问某个对象是否在某个Set中。
TreeSet默认排序是字典序的，如果想要按照字母序排列，可以向TreeSet的构造器传入String.CASE_INSENTIVE_ORDER比较器。

## 11.11 Queue

队列是一种典型的先进先出的容器。常被当做一种可靠的将对象从程序的某个区域传输到另一个区域的途径。在并发编程中特别重要。
LinkedList提供了方法以支持队列的行为，并且实现了Queue接口。

### 11.11.1 PriorityQueue

先进先出描述了最典型的队列规则。队列规则是指在给定一组元素的情况下，确定下一个弹出元素的规则。
优先级队列声明下一个弹出元素是最需要的元素（最高的优先级）。当在PriorityQueue上调用offer方法来插入一个对象时，这个对象将会在队列中被排序。默认是自然顺序，但是可以提供自己的Comparator来修改这个顺序。

## 11.12 Collection和Iterator

使用接口描述可以使我们能够创建更通用的代码。在C++中，并没有其容器的任何公共基类，所有的共性是通过迭代器达成的。Java中，绑定在一起，因为实现Collection就意味着需要提供iterator方法。
当你要实现一个不是Collection的外部类时，由于要实现Collection接口可能非常困难，因为使用Iterator就会非常吸引人。
虽然实现Collection和实现iterator，代价只是略有减少，但是如果你的类已经继承了其他，那么这种情况，创建迭代器就会显得容易多了。

## 11.13 Foreach与迭代器

Java SE5引入Iterable接口，该接口包含一个能够产生Iterator的iterator方法，并且Iterabale接口被foreach用来在序列中移动。任何实现了Iterabel的类，都可以用在foreach语句中。

foreach语句可以用于数组或者其他任何Iterable,但是这并不意味着数组肯定也是一个Iterabel，而任何自动包装也不会发生。

### 11.13.1 适配器方法惯用法

* 适配器方法 适配器设计模式

意识到Arrays.asLIst()产生的List对象会使用底层数组作为其物理实现是很重要的，只要你执行的操作会修改这个List，并且你不想原来的数组被修改，那么就应该在另一个容器中创建一个副本。
