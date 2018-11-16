---
title: markdown语法格式书写博客
tags:
  - 日记
  - 学习
  - 工作笔记
categories: web技术(Markdown语法)
abbrlink: 21f6d854
date: 2015-02-10 10:05:42
---
**Raysnote**是一款简洁而优秀的在线笔记工具，同时支持HTML富文本编辑和markdown语法，界面简洁优雅，中文排版优秀，具有优秀的书写和阅读体验。<!-- more -->
 

> Raysnote，为笔记而生！

-------------------

## 使用Raysnote可以做什么
可用Raysnote可以便捷高效地书写和整理笔记，同时可以享受优雅的阅读体验。

> - 十分简洁和优雅的记笔记和阅读的界面
> - 简洁和高效地管理笔记
> - 记笔记同时支持HTML富文本编辑和Markdown语法
> - 笔记支持代码高亮
> - 笔记支持MathJax公式
> - 优雅美观的中文排版，注重阅读体验
> - 一键保存网络文章，一致排版、十分方便

## Markdown语法简介

> Markdown 是一种轻量级标记语言，它允许人们使用易读易写的纯文本格式编写文档，然后转换成格式丰富的HTML页面。    
—— [维基百科](https://zh.wikipedia.org/wiki/Markdown)

例如本文，使用简单的符号可以标识不同的标题，将某些文字标记为**粗体**， *斜体*， ~~删除线~~，还可以插入代码`puts "helloworld"`，更高级地还可以插入表格、代码块和公式，例如：

### 代码块

```c
#include <stdio.h>

int main() {
   int a = 23, b = 47;
   
   printf("Before. a: %d, b: %d\n", a, b);
   a ^= b;
   b ^= a;
   a ^= b;
   printf("After.  a: %d, b: %d\n", a, b);
   return 0;
}

```
> 其他语言的代码高亮示例请查看文档[Raysnote代码高亮示例](http://raysnote.com/advise/notes/d955bad3fde04cc8860e9d105f2b1334)。

### MathJax 公式[^math](http://www.mathjax.org/)
$$   P(E)   = {n \choose k} p^k (1-p)^{ n-k}   $$

### 表格
| 商品         |     价格     | 数量  |
| :----------- | -----------: | :--: |
| Macbook Pro  | 1,299.00 USD |  8   |
| iPhone 5S    | 674.97 USD   |  128  |
| iPad Air     |    1.00 USD  | 1024  |


## 相关链接
- [Markdown简明语法速成](http://raysnote.com/markdown-get-started)
- [数学公式示例](http://raysnote.com/math-examples)
- [代码高亮示例](http://raysnote.com/code-examples)
- [反馈和建议](http://raysnote.com/advise)

--------
感谢您阅读本帮助文档，希望Raysnote能够帮助到您，祝您使用愉快！

[^math]: 关于MathJax公式的详细文档请查看：http://www.mathjax.org/