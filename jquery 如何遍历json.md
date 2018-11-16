---
title: jquery 如何遍历json数据
tags:
  - 日记
  - 学习
  - 工作笔记
categories: web技术(Jquery遍历)
abbrlink: 701232c9
date: 2015-04-10 10:05:42
---

js循环遍历后台返回的数据,然后拼接HTML代码动态添加到DOM结构中,是前端开发中经常遇到的,自己在接触过的项目中经常有这种需求,用jquery来循环就更加方便了,后台返回给我们的json数据,我们只需要使用jquery的each方法很容易遍历.<!-- more -->

## json数据格式

```json
var obj = { 
        "status":1, 
        "bkmsg":"\u6210\u529f", 
        "bkdata":["\u5415\u5c1a\u5fd7","1387580400","\u6dfb\u52a0\u8bb0\u5f55"] 
    } 
```

## jquery循环遍历json的方法

ajax请求：

```js
$.ajax({ 
        url: '/path/to/file', 
        type: 'GET', 
        dataType: 'json', 
        data: {param1: 'value1'}, 
        success: function (obj){  
            //遍历obj 
        } 
    })  
```
返回的内容在success的函数里面，所有的遍历操作都是在这里面操作的： 

### for循环：
```js
var obj = { 
        "status":1, 
        "bkmsg":"\u6210\u529f", 
        "bkdata":["\u5415\u5c1a\u5fd7","1387580400","\u6dfb\u52a0\u8bb0\u5f55"] 
    } 
  
    if (obj.status == 1) { 
        for (var i = 0; i < obj.bkdata.length; i++) { 
            console.log(obj.bkdata[i]); 
        }; 
    }else{ 
        alert("数据有误~"); 
    }; 
```



### for in 循环

```js
    for(x in obj.bkdata){ 
        //x表示是下标，来指定变量，指定的变量可以是数组元素，也可以是对象的属性。 
        console.log(obj.bkdata[x]); 
    }  
```
### 元素 each方法

```js
 if (obj.status == 1) { 
        $(obj.bkdata).each(function(index,item){ 
            //index指下标 
            //item指代对应元素内容 
            //this指代每一个元素对象 
            //console.log(obj.bkdata[index]); 
            console.log(item); 
            //console.log($(this)); 
        }); 
    }else{ 
        alert("数据有误~"); 
    };   
```

### jquery each方法


```js
$.each( obj.bkdata, function(index,item){ 
        console.log(item); 
    }); 
```