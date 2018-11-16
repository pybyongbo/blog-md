---
title: Ajax跨域请求之jsonp
tags:
  - ajax 跨域
  - jsonp
categories: web技术(跨域)
abbrlink: ad03a2be
date: 2016-06-05 15:36:49
---

## 什么是JSONP
一个众所周知的问题，Ajax直接请求普通文件存在跨域无权限访问的问题，甭管你是静态页面、动态网页、web服务、WCF，只要是跨域请求，一律不准；

为了便于客户端使用数据，逐渐形成了一种非正式传输协议，人们把它称作JSONP，该协议的一个要点就是允许用户传递一个callback参数给服务端，然后服务端返回数据时会将这个callback参数作为函数名来包裹住JSON数据，这样客户端就可以随意定制自己的函数来自动处理返回数据了。
<!-- more -->

## 同源策略

ajax之所以需要“跨域”，罪魁祸首就是浏览器的同源策略。即，一个页面的ajax只能获取这个页面相同源或者相同域的数据。

如何叫“同源”或者“同域”呢？——'协议'、'域名'、'端口号'都必须相同。

根据同源策略，我自己做的一个网页 http://localhost:8080/test.html 就无法通过ajax直接获取 http://google.com 的数据。

![jsonp报错](/uploadimg/demo_jsonp.png "jsonp报错")

大家想想，这样其实也有道理。如果没有同源策略，你我都可以随便通过ajax直接获取其他网站的信息，这还不乱套了。。。我自己做一个搜索界面，搜索时直接用ajax从百度获取数据，那不成了小偷了。。。

但是跨域访问是少不了的，mail.163.com 的网页可能需要从 news.163.com 域下获取新闻信息，那怎么办？——开始咱们的'跨域之旅'。（当然用iframe也可以实现）

## 从“盗链”说起

互联网的许多网站之间图片相互盗链，A网站网页的img.src直接链接到B网站的图片地址，这是常有的事儿。说到“盗链”，大家第一想到的可能是如何去防止盗链，今儿咱不管那个。

你再想想“盗链”和“同源策略”这两个词之间有什么关系？——对，矛盾！既然都“同源策略”了，怎么还能“盗链”呢？

世间万物都有矛盾，有矛盾了照样可以和谐共处，并不一定非要你死我活。

重点： 'img'的src（获取图片），'link'的href（获取css）,'script' 的src（获取javascript）这三个都不符合同源策略，它们可以跨域获取数据。因此，你可以直接从一些cdn上获取jQuery，并且你网站上的图片也随时可能被别人盗用，所有最好加上水印！

而我们今天的主角——jsonp——就是因为'script'的src不符合同源策略而来的。

## JSONP

例如，域名 a.com 下有一个 a.com/test.html 网页，域名 b.com 下有一个 b.com/data.html 网页和 b.com/alert.js 文件。

引导第一步：简单引用js
编写 b.com/alert.js 如下：


```javascript
alert(123);
```

对 a.com/test.html 编写如下代码：
```javascript
<script type='text/javascript' src='http://b.com/alert.js'/>
```

运行 a.com/test.html，结果很明显，就是弹出 【123】 。

### 引导第二步：引用js返回数据

将 b.com/alert.js 修改为：
```javascript
myFn(100);
```

将 a.com/test.html 修改为：

```javascript

    function myFn ( data ) {
        alert( data + 'px' );
    }
<script type='text/javascript' src='http://b.com/alert.js'/>
```

运行 a.com/test.html，结果是弹出【 100px 】，这个应该也没有什么疑问。

### 引导第三步：已经跨域成功！

第二步中，如果data——即100——是我要跨域在b.
com下获取的一个数据，那么咱们这不就是已经实现跨域请求了吗！！！
把这个过程再清晰的捋一遍：

> - script的src不符合同源策略；
> - 我通过给script的src赋值一个跨域的文件的网址（可能不是一个js文件），这个文件返回的字符串，浏览器会当作javascript来解析；
> - 而这段javascript中，就可以包含着我所需要的跨域服务器端的数据；
> - 最后，我在本页面定义一个myFn函数用来展示数据，而这段javascript中就可以直接调用myFn函数；

### 引导第四步：引用html格式

script的src不一定仅仅指向javascript文件，可以指向任何地址。例如：
将 a.com/test.html 修改为：

```javascript
    function myFn ( data ) {
        alert( data + 'px' );
    }

<script type='text/javascript' src='http://b.com/data.html'/>
```

将 b.com/data.html 编写为：（注意，data.html中就写以下一行代码，多了不写）

```javascript
myFn(100); 
```

运行 a.com/test.html ，结果依然是【 100px 】
其中，“100”就是我们要跨域请求的数据。

### 引导第五步：动态数据

如果要请求的数据是动态的，那就要在动态页面中编写。
那么我们就让 a.com/test.html 去调用一个动态的aspx页面：

```javascript
    function myFn ( data ) {
        alert( data + 'px' );
    }
<script type='text/javascript' src='http://b.com/data.aspx?callback=myFn'/>
```

大家注意，我们在 src 地址中增加了“?callback=myFn”，意思是把'显示数据的函数也动态传过去了'，而第二步、第四步都是静态的写在被调用的文件中的。

至于callback参数后台如何接收，如何使用，请接着看：
在 b.com 下增加一个 b.com/data.aspx 页面，后台代码如下：

```c
    protected void Page_Load(object sender, EventArgs e)
        {
            if (this.IsPostBack == false)
            {
                string callback = "";
                if (Request["callback"] != null)
                {
                    callback = Request["callback"];

                    //服务器端要返回的数据
                    string data = "1024";

                    Response.Write(callback + "(" + data + ")");
                }
            }
        }
```
代码很简单，获取callback参数，然后组成一个函数的形式返回。如果“b.com/data.aspx?callback=myFn”调用的话，那么返回的就是" myFn(1024) "。

返回的数据变成动态的了（“1024”），前端页面用于显示数据的函数也编程了动态的了（“callback=myFn”），但是归根结底，形式还是一样的。

### 引导第六步：调用封装

a.com/test.html 中，仅仅有一个*script*静静的躺在那里，执行一次之后，就没有作用了。

而实际情况是，a.com/test.html 中，可能随着用户的操作发生若干次的调用。怎么办？——动态增加呗。

```javascript
    function addScriptTag(src) {
        var script = document.createElement("script");
        script.setAttribute("type", "text/javascript");
        script.src = src;
        document.body.appendChild(script);
    }

    function myFn (data) {
        alert(data + 'px');  
    }

    //需要调用时：
    //addScriptTag('b.com/data.aspx?callback=myFn');
```

## 总结
以上层层描述的就是JSONP，你不必去记住它的定义，看明白了上述文字，就全能理解。
** 重点在于:同源策略`<script>`的src不属于同源策略,通过`<script>`的src指向的文件返回服务器端数据。**
OK,就这些啦~
