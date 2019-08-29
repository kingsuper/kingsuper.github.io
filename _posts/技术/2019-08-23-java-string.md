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

