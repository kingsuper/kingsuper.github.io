---
layout: post
title: Java 编程思想学习笔记-字符串
category: 技术
---
## 13.1 不可变String

String对象是不可变的。每一个看起来会修改String值的方法， 实际都是创建了一个全新的String对象，以包含修改后的字符串内容。

## 13.2 重载 “+”和StringBuilder

使用"+"连接字符串，编译器自动创建了一个StringBuild对象，用以构造最终的String，并为每个字符串调用append方法，最终toString输出。
但是当你编写toString方法时，如果字符串操作比较简单，那么可以信赖编译器为你优化。但是如果使用循环，那么最好自己创建一个StringBuilder对象，来构造最终的结果。

如果拿不准使用哪种方式，可以随时用javap来分析程序。StringBuilder提供丰富而全面的方法。StringBuilder是Java SE5引入的，之前使用的StringBuffer。后者是线程安全的，因此开销会大一些。

## 13.3 无意识的递归

如果在toString函数中，打印this，可能会导致递归。打印this时，尝试调用toString方法，然后进入循环递归。如果要打印对象地址，可以使用Object.toString(),即super.toString()。

## 13.4 String上的操作

见API文档。当需要改变字符串的内容时，String类的方法都会返回一个新的String对象。如果内容没有变化，通过只是指向元对象的引用。

## 13.5 格式化输出

### 13.5.1 printf()

### 13.5.2 System.out.format()

format和printf等价的，它们只需要一个简单的格式化字符串，加上一串参数。

### 13.5.3 Formatter类

java,所有新的格式化功能都是由java.util.Formatter类处理，负责翻译格式化字符串和数据。当创建Formatter对象时，需要传递一些信息，告诉往哪里输出。

### 13.5.4 格式化说明符

%[argument_index$][flags][width][.precision]conversion

width控制尺寸，Formatter对象通过必要时增加空格，来确保一个域达到某个长度，通过"-"标志来改变对齐的方向。
precision用来致命最大尺寸，String时，表示打印输出字符的最大数量。应用于浮点数时，表示小数要显示出来的位数。

### 13.5.5 Formatter转换

fomatter可以完成一些类型的转换。特别要注意的是，除了Boolean型，其他类型,主要不是null，转换为boolean的结果永远是true，即使是数字0，转换结果也是true。

### 13.5.6 String.format()

参考了sprintf的方法，生成格式化的String对象。静态方法，接收和formate一样的参数，返回一个String对象。
