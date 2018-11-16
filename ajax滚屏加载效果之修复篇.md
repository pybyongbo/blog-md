---
title: ajax滚屏加载效果之修复篇
tags:
  - ajax 滚动加载
  - 工作笔记
  - 兴趣研究
categories: web技术(Ajax加载)
abbrlink: 4b094186
date: 2015-05-17 11:03:53
---

记得上周发表过一篇关于php分页函数的封装和相关ajax分页效果的探讨的文章.但是之后在测试的过程中发现最后的一个滚动加载的ajax分页效果有个bug.
加载到页面的数据不是完全正确,有时候还会有重复加载的一种情况出现.本周末在家里看腾讯的前端团队的博客的时候,发现文章列表也用了滚屏加载的技术,于是就趁着周末有时间,恰巧有碰到这个问题,就重新研究了一下.<!-- more -->

-------------------

打开腾讯设计团队的博客:[腾讯ISUX 社交用户体验设计](http://isux.tencent.com/)查看了下页面中的源代码,看了下它的这种滚屏加载实现的思路.
事先定义了一个变量:*var loading_flag=false*;也就是我们程序中常说的加个锁.在没有数据的时候,将这个变量变为false.它的代码主要截图如下:

## 腾讯ISUX博客页面滚屏效果
![腾讯ISUX博客页面滚屏](/uploadimg/ajax_scroll4.jpg "腾讯ISUX博客页面滚屏")

之后仿照他的这个思路.我自己也把原先我写的代码做了相关的更改.更改之后没有明显的bug了.但是就是还有一个问题是,当加载完毕数据后,如果还在不断的拖动滚动条的话,打开firebug查看请求的地址,还是有相关的请求的.至不过没有数据显示返回显示到页面上面.暂时还不知道有没有什么其他的很好的解决方案.

请看如下截图:
## 数据加载完毕firebug查看相关请求
![数据加载完毕firebug查看相关请求](/uploadimg/ajax_scroll5.jpg "数据加载完毕firebug查看相关请求")

如果你也遇到过相关的问题欢迎探讨.关于页面有我的相关联系方式.

最后我修复之后的效果没有出现数据重复加载的bug了,基本上是按照拖动滚动条加载对应的分页数据显示在页面上面.

附上测试截图和demo地址:

## ajax滚屏加载修复bug

![ajax滚屏加载修复bug截图1](/uploadimg/ajax_scroll1.png "ajax滚屏加载修复bug截图1")

## ajax滚屏加载修复bug

![ajax滚屏加载修复bug截图2](/uploadimg/ajax_scroll2.png "ajax滚屏加载修复bug截图2")

## ajax滚屏加载修复bug

![ajax滚屏加载修复bug截图3](/uploadimg/ajax_scroll3.png "ajax滚屏加载修复bug截图3")

## 测试案例链接
- [ajax滚动加载之修复效果地址](http://demo.901web.com/ajaxpage/ajax_page_scroll_fixed/index.php)