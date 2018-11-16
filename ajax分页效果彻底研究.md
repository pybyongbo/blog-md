---
title: ajax分页效果彻底研究
tags:
  - 日记
  - 学习
  - 工作笔记
categories: web技术(Ajax加载)
abbrlink: 9205b99c
date: 2015-05-10 21:23:02
---
ajax(jquery,php,json数据格式)分页效果的彻底研究.周末放假休息,在家没有什么事情,就研究了下ajax分页的效果.虽然折腾了蛮长时间,但是通过自己的思考和整理,还是学到了不少的知识.

其中涉及到的知识点还蛮多的.看似一个简单的效果还是的多多尝试.就一个普通的ajax分页效果,我尝试着扩展了下网上常见的一些效果,也涉及到一些基础的php和数据库知识.<!-- more -->

## 学习了下php的分页相关知识.


## 扩展的分页效果有:

> - 普通的文本+数字点击分页效果
> - 通过点击加载更多按钮点击加载效果.
> - 通过判断滚动条位置是否达到底部从而加载数据,类似QQ空间的效果.

## 所运用到的相关基础知识点:

> - (1).Jquery AJAX POST与GET之间的区别,以及data参数的传递.
> - (2).Jquery循环json数据格式的方式有那些.(jquery 如何遍历json).
> - (3).Jquery中的ajax方法发送请求前,请求完成后相关的回调函数和操作.
> - (4).php中代码的延迟执行(sleep函数),数组,json_encode函数将php数组转换成json数据.
> - (5).Jquery中的append方法,show(),hide()显示影藏等等.
> - (6).Jquery中的相关事件方法,点击,拖动滚动条,如何给元素绑定事件不同方式,给动态生成的元素绑定事件.
> - (7).后台php文件可以返回json格式的数据,也可以直接返回html代码,前台直接append即可.

---
## 主要实现思路和相关的代码
---

(1).php中分页的实现思路和不同格式的分页方式调用.

首先建立数据库连接文件

**代码块(数据库连接文件)**

```php
<?php
$host="localhost";
$db_user="root";
$db_pass="root";
$db_name="kcdns";
$timezone="Asia/Shanghai";

$link=mysql_connect($host,$db_user,$db_pass);
mysql_select_db($db_name,$link);
mysql_query("SET names UTF8");

header("Content-Type: text/html; charset=utf-8");
date_default_timezone_set($timezone); //北京时间
?>

```

**代码块(分页函数)**

```php
<?php
/**
 * $_type=1数字分页 $_type=2文本分页 $_type=3 文本+数字
 * @param 分页函数_paging  $_type
*/

function _paging($_type) {
	global $page,$pagenum,$_num,$pagesize;
	if($_type == 1) {
		echo '<div id="page_num">';
		echo '<ul>';	
				 for($i=0;$i<$pagenum;$i++) {
				 	if($page==($i+1)) {
				 		echo '<li><a href="index.php?page='.($i+1).'" class="selected">'."[".($i+1)."]".'</a></li>';
				 	} else{
				 		echo '<li><a href="index.php?page='.($i+1).'">'.($i+1).'</a></li>';

				 	}
				 }
			echo '</ul>';		
		echo '</div>';
		
	} elseif($_type == 2) {
	
		echo '<div id="page_text">';
		echo '<ul>';
				echo '<li>共有<strong> '. $_num.' </strong>条数据</li>';
				echo '<li>'. $page.' /' .$pagenum.' 页</li>';
			
				 
				if($page ==1) {
				 echo '<li>首页</li>';
				 echo '<li>上一页</li>';
				} else{
					
					 echo '<li><a href="index.php">首页</a></li>';
				     echo '<li><a href="index.php?page='.($page-1).'">上一页</a></li>';
					
				}
				
			if($page == $pagenum) {
				
					 echo '<li>下一页</li>';
					 echo '<li>尾页</li>';
					 echo "\n";
					
				} else {
					 
				     echo '<li><a href="index.php?page='.($page+1).'">下一页</a></li>';
				     echo "\n";
				     echo '<li><a href="index.php?page='.$pagenum.'">尾页</a></li>';
				     echo "\t\t";
				}

		echo '</ul>';
		echo '</div>';
		
		
	} elseif($_type == 3) {

		echo '<div id="page_num_text">';
		echo '<div class="info"><strong>共 '.$_num.' 条数据</strong><em>每页显示<font color="red">'.$pagesize.'</font>条,</em><span>当前第'.$page.'页/共'.$pagenum.'页</span></div>';
			echo '<ul>';

			if($page ==1) {

				echo '<li>首页</li>';
				echo '<li>&lt</li>';
			} else{ 
				echo '<li><a href="index.php" title="index.php" rel="1">首页</a></li>';
				echo '<li><a href="index.php?page='.($page-1).'" title="index.php?page='.($page-1).'" rel='.($page-1).'>&lt</a></li>';
			  
			}


			 if ($pagenum > 1) {

				   for($i=1;$i<=$pagenum;$i++) {
				        if($i==$page) {
				            echo ' <li style="color:red">[',$i,']</li>';
				        } else {
				            echo '<li> <a href="index.php?page='.$i.'" title="index.php?page='.$i.'" rel='.($i).'>'.$i.'</a></li>';
				        }
				    }
			}


			if($page ==$pagenum ) {

				echo '<li>&gt</li>';
				echo '<li>尾页</li>';

			} else {
				  	 echo '<li><a href="index.php?page='.($page+1).'" title="index.php?page='.($page+1).'" rel='.($page+1).'>&gt</a></li>';
				     echo "\n";
				     echo '<li><a href="index.php?page='.$pagenum.'" title="index.php?page='.$pagenum.'" rel='.($pagenum).'>尾页</a></li>';
			}

			echo '<ul>';
		echo '</div>';

		
	} 
	
}
?>

```
**代码块(前台模版页面)**

```php
<?php
require_once('../include/connect.php'); 
require_once('../include/function.php'); 
$pagesize=4; //每页显示的记录数
//确定当前页数 $p 参数
$page = @$_GET['page']?$_GET['page']:1;

//数据指针
$offset = ($page-1)*$pagesize;

$result=mysql_query("select title,thumb,description,updatetime,url from v9_news order by id desc limit $offset , $pagesize");


//计算新闻总数
$count_result = mysql_query("SELECT count(*) as count FROM v9_news");
$_num=$count_result;

$count_array = mysql_fetch_array($count_result);


$_num = $count_array[0];
//计算总的页数
$pagenum=ceil($count_array['count']/$pagesize);

?>
```

**下面直接循环读取数据,调用写好的分页函数即可,具体的实例可以查看如下链接,对比地址栏观看**

[php分页函数实例链接(数字+文本)](http://demo.901web.com/ajaxpage/php_page1/index.php)


## 下面将介绍普通的ajax分页效果,点击分页ajax加载,POST方式

**代码块(主要Javascript代码)**

```Javascript
var curPage = 1; //当前页码
var total,pageSize,totalPage;
//获取数据
function getData(page){ 
	$.ajax({
		type: 'POST',
		url: 'data.php',
		data: {'pageNum':page},
		dataType:'json',
		beforeSend:function(){
			
			$(".loading").css({"display":"block","margin":"10px auto","text-align":"center","height":"128px"});
			$(".news_list").fadeOut("fast");
		},
		success:function(json){
			console.log(json);
			$(".news_list").empty();
			total = json.total; //总记录数
			pageSize = json.pageSize; //每页显示条数
			curPage = page; //当前页
			totalPage = json.totalPage; //总页数
			var html = "";
			var list = json.list;
			$.each(list,function(index,array){ //jquery的each方法遍历json数据列
				
				html += "<li>"+
							"<div class='date'><span class='time'>"+array['updatetime']+"</span></div>"
							+"<div class='thumb'><a href='"+array['url']+"'><img src="+array['thumb']+"></a></div>"
							+"<div class='descripation'><h2><a href='"+array['url']+"'>"
								+array['title']+
								"</a></h2><p>"+array['description']+"...<a href='"+array['url']+"'>[查看全文]</a></p></div></li>";

				

			});
			$(".news_list").append(html);

		},

		complete:function(){ //生成分页条
			//getPageBar(1);
			getPageBar(3);
			$(".news_list").fadeIn("fast")
			$(".loading").css({"display":"none"});

		},
		error:function(){
			alert("数据加载失败");
		},
	});
}


function getPageBar(type){

	//页码大于最大页数
	if(curPage>totalPage) curPage=totalPage;

	//页码小于1
	if(curPage<1) curPage=1;

	if(curPage>=totalPage) curPage=totalPage;

	pageStr = "<li>共"+total+"条</li><li>"+curPage+"/"+totalPage+"</li>";

	//type==1表示文本分页,type==2表示数字分页
	if(type==1) {

		//如果是第一页
		if(curPage==1){
			pageStr += "<li>首页</li><li>上一页</li>";
		}else{
			pageStr += "<li><a href='javascript:void(0)' class='pajax' rel='1'>首页</a></li><li><a href='javascript:void(0)' class='pajax' rel='"+(curPage-1)+"'>上一页</a></li>";
		}
		
		//如果是最后页
		if(curPage>=totalPage){
			pageStr += "<li>下一页</li><li>尾页</li>";
		}else{
			pageStr += "<li><a href='javascript:void(0)' class='pajax' rel='"+(parseInt(curPage)+1)+"'>下一页</a></li><li><a href='javascript:void(0)' class='pajax' rel='"+totalPage+"'>尾页</a></li>";
		}

	} else if(type==2) {

		for(i=1;i<=totalPage;i++) {

			if(i == curPage) {

				pageStr += "<li><a href='javascript:void(0)' class='pajax current' rel='"+i+"'>"+(i)+"</a></li>";

			} else {

				pageStr += "<li><a href='javascript:void(0)' class='pajax' rel='"+i+"'>"+(i)+"</a></li>";

			}
		}

	} else if(type==3) {

		if(curPage==1){
			pageStr += "<li>首页</li><li>&lt</li>";
		} else {
			pageStr += "<li><a href='javascript:void(0)' class='pajax' rel='1'>首页</a></li><li><a href='javascript:void(0)' class='pajax' rel='"+(curPage-1)+"'>&lt</a></li>";

		}

		//中间部分数字分页显示
		if(totalPage>1) {

			for(i=1;i<=totalPage;i++) {

			if(i == curPage) {

				pageStr += "<li><a href='javascript:void(0)' class='pajax current' rel='"+i+"'>"+"["+i+"]"+"</a></li>";

			} else {

				pageStr += "<li><a href='javascript:void(0)' class='pajax' rel='"+i+"'>"+(i)+"</a></li>";

				}
			}

		}

		//最后一页的情况
		if(curPage==totalPage){
			pageStr += "<li>&gt</li><li>尾页</li>";
		}else{
			pageStr += "<li><a href='javascript:void(0)' class='pajax' rel='"+(parseInt(curPage)+1)+"'>&gt</a></li><li><a href='javascript:void(0)' class='pajax' rel='"+totalPage+"'>尾页</a></li>";
		}


	}
	
	$(".pagelist").html(pageStr);
}

$(function(){
	getData(1);
	$("#page_num_text").on('click','.pajax',function(){
		var rel = $(this).attr("rel");
		if(rel){
			getData(rel);
		}
	});
});

```

## 普通点击分页ajax加载
![普通点击分页ajax加载](/uploadimg/ajax1.png "普通点击分页ajax加载")

---
[php普通点击分页ajax加载](http://demo.901web.com/ajaxpage/ajax_page1/index.php)

## 普通点击按钮ajax加载
![普通点击按钮ajax加载](/uploadimg/ajax2.jpg "普通点击按钮ajax加载")

---
[普通点击按钮ajax加载](http://demo.901web.com/ajaxpage/ajax_page_click/index.php)

## 滚动到底部ajax加载
![滚动到底部ajax加载](/uploadimg/ajax3.jpg "滚动到底部ajax加载")

---
[滚动到底部ajax加载](http://demo.901web.com/ajaxpage/ajax_page_scroll/index.php)

