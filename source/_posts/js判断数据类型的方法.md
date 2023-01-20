---
title: JS判断数据类型的方法
date: 2022-11-21 20:02:05
tags: 
 - 前端
 - 面试
 - javascript
cover: /image/cover12.jpg
categories: 
 - javascript
---
## typeof
1. 作用：用于判断**基本类型**的值
2. 结果(返回字符串)：
 -- 判断基本类型的值均准确，除开null
 -- 引用类型都为Object,除开function
 ```
 typeof ''             // string
 typeof NaN            // number
 typeof undefined      // undefined
 typeof null           // object
 typeof []             //object
 typeof newFunction()  // function
 ```
## instanceof
1. 作用：
 -- 用于引用类型判断(准确)
 -- 是否为其的实例
2. 用法： A instanceof B  判断A是否为B的实例
3. 原理
instanceof 在查找过程中会遍历左侧变量的原型链，看是否存在右侧变量的prototype
```
引用类型 instanceof Object   // true
基本类型 instanceof Object   // false
```

## Object.prototype.toString(最准确)
1. 概念：toString()是Object原型上的方法，可以返回调用者的具体类型
2. 使用方式：``Object.prototype.toString.call(需判断变量)``
**原因：** 有很多类型自身也带有toString()方法，所以要用.call()让其指向Object，使用Object的toString()方法
```
Object.prototype.toString.call('')                 // [object String]
Object.prototype.toString.call(1)                  // [object Number]
Object.prototype.toString.call(undefined)          // [object Undefined]
Object.prototype.toString.call(null)               // [object Null]
Object.prototype.toString.call(new funciton())     // [object Function]
Object.prototype.toString.call(new RepExp())       // [object RegExp]
Object.prototype.toString.call([])                 // [object Array]
Object.prototype.toString.call(new Date())         // [object Date]
Object.prototype.toString.call(new RepExp())       // [object RegExp]
```
