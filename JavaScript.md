<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [JavaScript](#javascript)
  - [1. 数组相关api](#1-%E6%95%B0%E7%BB%84%E7%9B%B8%E5%85%B3api)
  - [2. 闭包](#2-%E9%97%AD%E5%8C%85)
    - [优点](#%E4%BC%98%E7%82%B9)
    - [缺点](#%E7%BC%BA%E7%82%B9)
    - [闭包的三个特性](#%E9%97%AD%E5%8C%85%E7%9A%84%E4%B8%89%E4%B8%AA%E7%89%B9%E6%80%A7)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


# JavaScript
## 1. 数组相关api
## 2. 闭包
闭包是能够访问另一个函数的变量的函数。使用闭包主要是为了设计私有的方法喝变量。
```js

function funA() {
  var a = 10;
  return function() {
        alert(a); // 访问funA中的变量a
  }
}

let b = funA();
b(); // 10
```
### 优点
避免全局变量污染
### 缺点
闭包常驻内存，会增大内存使用量，使用不当很容易造成内存泄漏。
### 闭包的三个特性
1. 函数嵌套函数。
2. 函数内部可以引用外部的参数喝变量。
3. 参数和变量不会被垃圾回收机制回收。

