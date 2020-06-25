# JavaScriptGuide
## 前言
借鉴于JavaGuide这个优秀的项目，自己在前端学习过程中，还没有发现在JS领域（或许由于自己见识有限）有类似的答疑、解惑的项目。因此，这个项目希望能够帮助到前端新人，能够有准确的回答。自己水平有限，如果有错误或不足的，欢迎各位的指正。

JavaGuide is such a nice project, so I just wanna create this repository, wishing it could help others.

## 目录

- [JavaScript基础](#javascript基础)
     - [1.原始值和引用值类型及区别](#1原始值和引用值类型及区别)
     - [2.undefined和null的区别](#2undefined和null的区别)
     - [3.JS中的内存泄漏](#3内存泄漏)
     - [4.JS中的垃圾回收（GC）](#4JS中的垃圾回收（GC）)
     - [5.浅拷贝与深拷贝](#5浅拷贝与深拷贝)
- [ES6](#java)
- [网络](#网络)
- [操作系统](#操作系统)
- [数据结构与算法](#数据结构与算法)**


## JavaScript基础

#### 1原始值和引用值类型及区别

**原始类型：**`Undefined`、`Null`、`Boolean`、`Number`、`String`、`Symbol`（es6）、`BigInt`（es10 chrome支持）


**引用类型：**`Object`

- **除了常用的Object，Array、Function等都属于特殊的对象**



#### 2undefined和null的区别

**undefined：**
表示“未定义”，是一个不正常的值。当一个变量申明了，却没有初始化时，其值默认为undefined。

**null：**
表示“空”，是一个正常的值。

- **typeof null  // 结果为object** 这是一个特例，原因有如下两种解释：
 - 一是认为，null表示一个空对象指针
 - 二是认为，这是一个遗留下来的bug。null的机器码全为0，导致typeof算法判断的时候将其判断为对象。

-  对于**未申明的变量**，直接使用会报 `Uncaught ReferenceError`的错误。但typeof 操作符对**未申明**和**未初始化**的变量返回的结果都是`undefined`。



