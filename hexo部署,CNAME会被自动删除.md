---
title: hexo部署后，CNAME会被自动删除，怎么办？
tags:
  - hexo文档
categories: web技术(Hexo技术)
abbrlink: 71d89490
date: 2015-02-10 21:23:02
---

有时候hexo部署后,CNAME会被自动删除

之前自己在部署发布的时候,就遇到过这个问题,每次部署之后,手动的在github上面手动的添加CNAME文件.

这时候我们可以将CNAME或README文件放在source文件夹下,以后每次hexo部署后,CNAME或README文件就不会消失啦.

这样的话每次就会生产CNAME文件了,不用每次手动的去添加啦.自己遇到过,特意在此记录下.
