---
title: javascript冷门知识点
date: 2021-07-11 17:51:26
tags:
  - javascript
---

# 为什么函数是 javascript 中的一等公民

如果某个编程语言的函数，可以和这个语言的数据类型做一样的事情，我们就把这个语言中的函数称为一等公民。
在 javascript 中，函数可以像对象一样拥有自己的属性和方法，也可以作为参数传递给另一个函数，甚至可以作为一个函数的返回值（这个特性要求 js 虚拟机必须实现闭包的特性）。

# javascript 中对象属性的访问顺序

```javascript
function Foo() {
  this[100] = "test-100";
  this[1] = "test-1";
  this["B"] = "bar-B";
  this[50] = "test-50";
  this[9] = "test-9";
  this[8] = "test-8";
  this[3] = "test-3";
  this[5] = "test-5";
  this["A"] = "bar-A";
  this["C"] = "bar-C";
}
var bar = new Foo();

for (key in bar) {
  console.log(`index:${key}  value:${bar[key]}`);
}
/**
index:1  value:test-1
index:3  value:test-3
index:5  value:test-5
index:8  value:test-8
index:9  value:test-9
index:50  value:test-50
index:100  value:test-100
index:B  value:bar-B
index:A  value:bar-A
index:C  value:bar-C
 * /
```

在 ECMAScript 规范中定义了数字属性应该按照索引值大小升序排列，字符串属性按照创建时的顺序升序排列。
数字属性成为排序属性（elements），字符串属性成为常规属性（properties）

# javascript 中的 fast、slow、in-object 属性

在 js 的对象中，访问速度最快的是 in-object 属性，他们直接存储在对象上面，但是 in-object 属性的数量取决于初始化对象的大小（可以理解为一般为 10 个）。
当属性数量超出 in-object 的 size 时，属性会被存储到 properties 中，在数量不大的情况下，properties 中的属性采用线性存储结构，有点是访问速度快，增加和删除的速度慢。在数量增大的情况下，properties 会转化为字典的存储结构，有点是增加和删除的速度快，访问的速度慢。

# 函数声明与函数表达式

```javascript
// 函数声明
function foo() {}

// 函数表达式
const foo = function () {};
```
