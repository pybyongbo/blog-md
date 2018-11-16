---
title: 移动端M站搜索历史记录小结
tags:
  - 移动端搜索
  - localstorage
categories: web技术(移动端M站)
abbrlink: 2595a846
date: 2017-04-29 19:33:49
---
## 写在前面
新的一年里,很久都没有写东西了,也没有更新自己的博客,2017年已经过去了快四分之一了.这一年感觉自己变得越来越懒惰了.今年来了就开始做公司的M站.差不多经历了一个多余的时间,一个从无到有的M站诞生了,东西虽然做出来了,然而却并不理想.各方面的原因都有吧.我也不想为自己的问题找借口了,好好总结下,希望在下次的项目中能够尽量少出问题,保证质量能够做得更好...

<!-- more -->

## 什么是localstorage
在HTML5中，新加入了一个localStorage特性，这个特性主要是用来作为本地存储来使用的,解决了cookie存储空间不足的问题(cookie中每条cookie的存储空间为4k),localStorage中一般浏览器支持的是5M大小,这个在不同的浏览器中localStorage会有所不同.在制作移动端页面的时候,我们可以放心的使用.移动端浏览器一般都支持这个属性(个人感觉类似PC端的cookies,存储数据的).


## 全局搜索的产品需求大体流程图

产品需求大体如下,之前单个的功能自己写过demo,但是整体的需求连接起来还是没有尝试过,以至于在这次开发中还是遇到了不少的坑,开始的时候对于存储历史搜索数据也是毫无头绪的,后来在同事的帮忙下才勉强的完成了该功能.感觉自己的基础还是比较薄弱,平常还是要加强多写写.

![产品全局搜索需求图](/uploadimg/search.png "需求流程图")

## 没有历史搜索的时候默认的排版

![搜索默认图](/uploadimg/s0.png "搜索默认图")

## 搜索下拉功能模块

搜索下拉监听文本框输入,请求接口,拿到数据做对应的渲染,没有数据做对应的提示即可.我在页面中对文本框写的是*keyup*事件,貌似感觉不太好,有时候接口返回数据有点慢,对于用户频繁的操作文本框中的内容,导致数据不太正确.该功能不复杂,但有很大的优化空间.

![搜索下拉图](/uploadimg/s1.png "搜索结果下拉图")


```javascript
    
    $("#txtColor").on("keyup", function(e) { 

             var event = e || {};
           
            if ($("#txtColor").val() == "") {

                $(".noinput_event").removeClass('none');
                $(".history_searchauto").remove();
                $(".dataEmpty").addClass('none');
                $(".search_b_deldte").addClass('none');

            } else {

                That.search();//请求后端搜索接口,返回数据.(下拉)
                $(".search_b_deldte").removeClass('none');

            }
            if (event.keyCode == 13) {

                That.mSearchAssociation(e);//后端接口,搜索结果列表
            }

        });
```


## 历史搜索功能

在文本框中输入了搜索关键词后,点击Enter键,进入搜索结果页面.然后进行的是下拉分页显示数据.则该条关键词搜索计入历史搜索记录.(如果历史搜索记录大于10条,则取最新的10条数据.)

![历史搜索图](/uploadimg/s2.png "历史搜索记录图")


对应相关的搜索结果页面:

![搜索结果图](/uploadimg/s3.png "搜索结果页图")


## 记录历史搜索和删除历史搜索记录

我们定义为只有按了Enter键后,进入了搜索结果页面,才计入历史搜索列表中,这里就是将搜索的关键词存入localstorage中了.目前所有的浏览器中都会把localStorage的值类型限定为string类型，这个在对我们日常比较常见的JSON对象类型需要一些转换.这个里面我们需要做一些相应的转换.我们存入的格式如下图所示,这里我也是第一次使用这个属性:

![localStrorage存储图](/uploadimg/s4.png "localStrorage存储格式图")

我们这里存储了对应搜索的关键词和URL地址.当我们从历史搜索记录点击过来的时候可以直接进行搜索.当我们已经搜索过相同的关键词的时候,我们就不要重复计入历史搜索记录了,在存储的时候还需要先判断是否已经有相同的关键词.(数组中重复的项目,进行判断indexOf,以及字符串的方法).

下面就是一些关键功能性的代码了.从localStorage中来取值,如果存在的话.

```javascript

     if (localStorage.getItem("array")) {

                var searchList = JSON.parse(localStorage.getItem("array"));
                var slen = searchList.length;
                if (slen > 0) {
                    //有历史搜索的:由JSON字符串转换为JSON对象
                    var html_str = "";
                    $(".history").removeClass('none');
                    for (var i = 0; i < slen; i++) {
                        var url=JSON.parse(searchList[i]).search_url;

                        if(url.indexOf("?q")==-1) {

                            url=url+"?q="+ JSON.parse(searchList[i]).keywords;
                        }
                        html_str += "<li><a href="+url+">" + JSON.parse(searchList[i]).keywords + "</a><img src='images/searchClose.png' class='del'></li>";
 
                    }

                    $(".history ul").append(html_str);
                    $(".icon_tips").addClass('none');
                    $(".history").removeClass('none');

                } else {

                    $(".icon_tips").removeClass('none');

                }

            }

```

清除单个历史记录,我们需要找到当前对应的索引.来删除这个对应的历史记录.

```javascript
        //单个历史记录清除:
        $("img.del").each(function(index, el) {
                
                $(el).off().on('tap click', function() {
                var searchList = JSON.parse(localStorage.getItem("array"));
                searchList.splice(index, 1); //删除对应的索引的历史记录
                localStorage.array = JSON.stringify(searchList);
                $(el).parent('li').remove();

                if($(".history li").length==0) {
                    $(".history").addClass('none');
                    $(".icon_tips").removeClass('none');
                }

            })
        }); 
  ```

清除所有历史记录,即清空localStorage里面对应存储搜索历史记录的值即可(localStorage.removeItem).

```javascript
    //清除所有历史记录点击事件
        $(".clearHistory").off().on('tap click', function() {


            localStorage.removeItem("array");
            $(".history ul li").remove();
            $(".history").addClass('none');
            $(".icon_tips").removeClass('none');

        })

```

最后重点的内容就是如何存储的localStorage为我们所需的那种格式呢.我们按了Enter键,从搜索下拉展示到搜索结果页面.肯定会有一个关键词带过来.文本框中搜索的关键词.

```javascript
        var keywords;
        if(fromurl.indexOf("?q")>-1) {
            keywords = LeadBase.GetQueryString("q");
        } else {
            keywords = localStorage.getItem("KEYWORDS");
        }
```

历史搜索记录存储localStorage函数方法:
```javascript
  getSaveHistroy:function(obj){
            var getSearchHistory = [];
            if (localStorage.array != null) {
                getSearchHistory = $.parseJSON(localStorage.array);
            }
            if (keywords != "null") {

                obj.keywords = keywords;
                obj.search_url = search_url+"?q="+keywords;
                var index = -1

                for(var i in getSearchHistory) {

                    if(keywords==$.parseJSON(getSearchHistory[i]).keywords){

                        index=i;
                        break;
                    }
                }
                
                if (index > -1) getSearchHistory.splice(index, 1);

                getSearchHistory.unshift(JSON.stringify(obj));

                getSearchHistory.splice(10, 1);

                localStorage.array = JSON.stringify(getSearchHistory);

            }

        }
```

localStorage中我们存储的格式如下:

```javascript
[
 "{\"keywords\":\"223\",\"search_url\":\"search_result.html?q=223\"}",
 "{\"keywords\":\"1\",\"search_url\":\"search_result.html?q=1\"}",
 "{\"keywords\":\"12\",\"search_url\":\"search_result.html?q=12\"}"
]
```

## 写在最后

对于这个历史搜索记录的小功能,虽然说不是很难,但是还是有很多的细节和注意点的,也体现了自己的基础知识还是不到位,有数组的添加,删除,去重复,字符串的查找,还有json的一些相关知识点等等.不过都是一些基础的东西,还是希望自己在写东西之前能够想清楚整个需求的逻辑,这样才能提高效率,少走弯路.