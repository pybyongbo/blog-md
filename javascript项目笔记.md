---
title: 'JS常用API,项目笔记汇总'
tags:
  - JS API
  - 工作笔记
categories: web技术(JS异步操作)
abbrlink: 40874f0
date: 2018-11-01 14:33:53
---

ES6的一些方法最近在项目中经常的用到,空闲的时候,自己对一些常用的API进行了整理和对比.虽然有的特效浏览器支持不是太友好,但是可以通过`babel`进行转换.还有一些常用的ES6方法,例如find,findIndex,以及一些数组的方法, 文档持续更新中...

<!-- more -->

#### 扩展运算符:三个...

  合并数组

```javascript
let arr1 = [1, 2], arr2 = [3, 4];
arr1.push(...arr2);//把 arr2合并到arr1的尾部,arr1改变
arr1.unshift(...arr2); //把 arr2合并到arr1的顶部部,arr1改变
[...arr1,...arr2]; //生成一个由arr1和arr2组成的新数组,原数组不变.
```
笔记截图:
![其他模块](/uploadimg/jsdemo1.gif "控制台截图")


#### 复制对象:
```javascript
let obj = {a:1};
{...obj,b:2} //返回一个新的对象,{a:1,b:2},obj对象不变
//等价于下面的写法
Object.assign({},obj,{b:2}) //返回一个新的对象,{a:1,b:2},obj对象不变
//另外 Object.assign还有一个用法
Object.assign(obj,{b:2})// 返回obj对象并且新增加了b属性：{a: 1, b: 2}  obj对象改变
// 由于使用的chrome浏览器还不支持第一种用法，只能演示Object.assign。项目中使用babel转换

```
笔记截图:
![其他模块](/uploadimg/jsdemo3.gif "控制台截图")
