# JavaScriptGuide
## 前言
借鉴于JavaGuide这个优秀的项目，自己在前端学习过程中，还没有发现在JS领域（或许由于自己见识有限）有类似的答疑、解惑的项目。因此，这个项目希望能够帮助到前端新人，能够有准确的回答。自己水平有限，如果有错误或不足的，欢迎各位的指正。

JavaGuide is such a nice project, so I just wanna create this repository, wishing it could help others.

## 目录

- [JavaScript基础](#javascript基础)
     - [1.原始值和引用值类型及区别](#1原始值和引用值类型及区别)
     - [2.undefined和null的区别](#2undefined和null的区别)
     - [3.JS中的内存泄漏](#3内存泄漏)
     - [4.JS中的垃圾回收机制（GC）](#4JS中的垃圾回收机制（GC）)
     - [5.浅拷贝与深拷贝](#5浅拷贝与深拷贝)
     - [6.判断数据类型的方法](#6判断数据类型的方法)
- [ES6](#es6)
     - [1.let&const&var的概念与区别](#1let&const&var的概念与区别)
     - [2.变量提升与暂时性死区](#2变量提升与暂时性死区)
     - [3.变量的结构赋值](#3变量的结构赋值)
     - [4.箭头函数及其this问题](#4箭头函数及其this问题)
     - [5.Symbol概念及其作用](#5Symbol概念及其作用)
     - [6.Set和Map数据结构](#6Set和Map数据结构)
- [HTML/CSS](#HTML/CSS)
     - [1.CSS权重及其引入方式](#1CSS权重及其引入方式)
     - [2.a标签全部作用](#2a标签全部作用)
     - [3.用CSS画三角形](#3用CSS画三角形)
     - [4.水平垂直居中](#4水平垂直居中)
     - [5.盒子模型及其理解](#5盒子模型及其理解)
     - [6.定位方式及其区别](#6定位方式及其区别)
     - [7.浮动模型及清除浮动的方法](#7浮动模型及清除浮动的方法) 
- [网络](#网络)
     - [1.OSI七层模型](#1OSI七层模型)
     - [2.http1.0&http1.1&http2.0](#2http1.0&http1.1&http2.0)
     - [3.HTTP建立持久连接的意义](#3HTTP建立持久连接的意义)
     - [4.HTTP报文的结构](#4HTTP报文的结构)
     - [5.HTTP状态码](#5HTTP状态码)
     - [6.Set和Map数据结构](#6Set和Map数据结构)
     - [7.Cookie与Session](#7Cookie与Session) 
- [操作系统](#操作系统)
	 - [1.线程与进程](#1线程与进程)
- [数据结构与算法](#数据结构与算法)
	 - [1.常见排序算法及时间复杂度](#1常见排序算法及时间复杂度)
	 - [2.DFS与BFS](#2DFS与BFS) 


## JavaScript基础

#### 1原始值和引用值类型及区别

**原始类型：**`Undefined`、`Null`、`Boolean`、`Number`、`String`、`Symbol`（es6）、`BigInt`（es10 chrome支持）


**引用类型：**`Object`

- **除了常用的Object，Array、Function等都属于特殊的对象**
- ** **

![Alt text](https://github.com/Jackie-Hung/JavaScriptGuide/blob/master/pictures/1593091870011.png)

**栈内存：**存储的值大小固定
**堆内存：**存储的值大小不固定
- 为什么str+=6结果为 "JackieHung6" ?
![Alt text](https://github.com/Jackie-Hung/JavaScriptGuide/blob/master/pictures/1593094167516.png)

- 为什么基本类型也有方法和属性，eg：str.length ?
基本包装类型：Boolean、Number、String
对于调用基本类型方法和属性时（eg，String为例），进行以下操作：
	- new 一个String类型的实例对象
	- 在实例对象上调用指定的方法
	- 销毁这个实例对象

#### 2undefined和null的区别

**undefined：**
表示“未定义”，是一个不正常的值。当一个变量申明了，却没有初始化时，其值默认为undefined。

**null：**
表示“空”，是一个正常的值。

- **typeof null  // 结果为object** 这是一个特例，原因有如下两种解释：
 - 一是认为，null表示一个空对象指针
 - 二是认为，这是一个遗留下来的bug。null的机器码全为0，导致typeof算法判断的时候将其判断为对象。

-  对于**未申明的变量**，直接使用会报 `Uncaught ReferenceError`的错误。但typeof 操作符对**未申明**和**未初始化**的变量返回的结果都是`undefined`。
#### 3JS中的内存泄漏
**内存泄露（memory leak）：**对于不再用到的内存，没有及时释放，就叫做内存泄漏

#### 4JS中的垃圾回收机制（GC）

- 引用计数
- 标记清除


#### 6判断数据类型的方法
- typeof
- instanceof
- Object.prototype.toString.call()
- constructor

## ES6
