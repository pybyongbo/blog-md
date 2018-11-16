---
title: HTTP强缓存和协商缓存
tags:
  - web前端缓存
  - 工作笔记
categories: web技术(前端缓存)
abbrlink: 59ab7e69
date: 2018-03-20 20:03:53
---

### 科普一
```html
从输入网址到页面呈现过程：
1、浏览器输入：`www.leadbank.com` 通过DNS(网域名称系统)解析成IP地址: `103.6.220.58`
2、客户端与服务器建立TCP/IP协议
3、传输层向服务器发送请求协议（HTTP/HTTPS/FTP等）
4、服务器响应请求，返回指定的URL数据或错误信息（如果设定重定向则重定向到新的URL地址）（Nginx反向代理）
5、客户端下载对应文件后：解析HTML以构建DOM树 –> 构建渲染树 –> 布局渲染树 –> 绘制渲染树，最后呈现给用户

> DNS解析优先顺序: 先读缓存--Hosts文件--路由缓存--DNS缓存--根域名
> DNS劫持：从DNS缓存数据库里找时被恶意改为其他的网址,所以请求到的是其他网址
> Nginx反向代理：客户端请求Nginx服务，Nginx请求应用服务器，返回数据给到客户端，访问量大的网站一般采用Nginx反向代理，确保一台服务器挂了还有其他服务器正常运行，用户依旧可以正常使用。
```
<!--more-->

![DNS实际解析图](http://upload-images.jianshu.io/upload_images/735918-19380e39f12aef7d.png?imageMogr2/auto-orient/strip)


### 科普二
```html
请求常见状态码：
200	OK	请求成功。
301	Moved Permanently	永久移动。请求的资源已被永久的移动到新URI，返回信息会包括新的URI，浏览器会自动定向到新URI。今后任何新的请求都应使用新的URI代替
302	Found	临时移动。与301类似。但资源只是临时被移动。客户端应继续使用原有URI
304	Not Modified	未修改。所请求的资源未修改，服务器返回此状态码时，不会返回任何资源。客户端通常会缓存访问过的资源，通过提供一个头信息指出客户端希望只返回在指定日期之后修改的资源
403	Forbidden	服务器理解请求客户端的请求，但是拒绝执行此请求
404	Not Found	服务器无法根据客户端的请求找到资源（网页）。通过此代码，网站设计人员可设置"您所请求的资源无法找到"的个性页面
405	Method Not Allowed	客户端请求中的方法被禁止
500	Internal Server Error	服务器内部错误，无法完成请求
501	Not Implemented	服务器不支持请求的功能，无法完成请求
502	Bad Gateway	充当网关或代理的服务器，从远端服务器接收到了一个无效的请求
503	Service Unavailable	由于超载或系统维护，服务器暂时的无法处理客户端的请求。延时的长度可包含在服务器的Retry-After头信息中
504	Gateway Time-out	充当网关或代理的服务器，未及时从远端服务器获取请求
505	HTTP Version not supported	服务器不支持请求的HTTP协议的版本，无法完成处理

```

---
### 浏览器缓存
> 前端最头疼的两大问题：`命名` 和 `缓存`

> - **什么是浏览器缓存？**   
> 浏览器缓存就是浏览器在本地磁盘对用户近期请求过的文档进行存储，当访问者再次访问同一页面时，浏览器可以直接从本地磁盘加载文档。

> - **缓存的优点？**  
> 减少重复数据的多次请求，减轻服务器负担  
> 提高网页加载速度，提升用户体验  
> 离线应用等  

> - **我们的痛点**  
> 当网站发生了更新的时候（如替换了css、js以及图片文件），浏览器本地仍保存着旧版本的文件，从而导致无法预料后果。

> - **如何更好的运用浏览器缓存？**   
？？？   
？？？

### 浏览器缓存的分类
> 浏览器缓存主要有两类：==缓存协商== 和 ==彻底缓存==，也有称之为 ==协商缓存== 和 ==强缓存== 。

> **浏览器在第一次请求发生后，再次请求时：**   
> 1、浏览器会先获取该资源缓存的header信息，根据其中的expires和cahe-control判断是否命中强缓存，若命中则直接从缓存中获取资源，包括缓存的header信息，本次请求不会与服务器进行通信；    
> 2、如果没有命中强缓存，浏览器会发送请求到服务器，该请求会携带第一次请求返回的有关缓存的header字段信息（Last-Modified/IF-Modified-Since、Etag/IF-None-Match）,由服务器根据请求中的相关header信息来对比结果是否命中协商缓存，若命中，则服务器返回新的响应header信息更新缓存中的对应header信息，但是并不返回资源内容，它会告知浏览器可以直接从缓存获取；否则返回最新的资源内容 

```javascript
// --Response Headers --
Accept-Ranges:bytes
Cache-Control:max-age=864000
Content-Encoding:gzip
Content-Length:6740
Content-Type:text/css
Date:Fri, 17 Nov 2017 02:43:40 GMT
ETag:"80ad-55deedca5b440-gzip"
Expires:Mon, 27 Nov 2017 02:43:40 GMT
Last-Modified:Tue, 14 Nov 2017 10:29:29 GMT
Server:Apache/2.4.10 (Unix) OpenSSL/0.9.8e-fips-rhel5
Vary:Accept-Encoding,User-Agent
// 案例地址：http://blog.csdn.net/wangjun5159/article/details/46912803

```

### 强缓存（Expires + Cache-Control）

> 强缓存是利用http的返回头中的Expires或者Cache-Control两个字段来控制的，用来表示资源的缓存时间。

> **Expires**  
> 响应值: `Expires:Mon, 27 Nov 2017 02:43:40 GMT`   
> 表示该资源失效的时间，在该时间内即命中缓存。（由于时间是绝对时间所以如果客户端时间和服务器误差较大会造成缓存错乱）

> **Cache-Control** `（高优先级）`  
> 响应值：`Cache-Control:max-age=864000`  
> 代表该资源有效时间为 `864000` 秒,也就是 `100` 天  
> 其他属性：  
`no-cache`：不使用本地缓存。  
说明: 需要使用缓存协商，先与服务器确认返回的响应是否被更改，如果之前的响应中存在ETag，那么请求的时候会与服务端验证，如果资源未被更改，则可以避免重新下载。    
`no-store`：直接禁止游览器缓存数据。  
说明：每次用户请求该资源，都会向服务器发送一个请求，每次都会下载完整的资源。  
`public`：可以被所有的用户缓存。   
说明：包括终端用户和CDN等中间代理服务器。  
`private`：只能被终端用户的浏览器缓存。   
说明：不允许CDN等中继缓存服务器对其缓存。  
**`注意：`**  
**`Cache-Control与Expires可以在服务端配置同时启用，同时启用的时候Cache-Control优先级高。`**

### 协商缓存
> 协商缓存就是由服务器来确定缓存资源是否可用，所以客户端与服务器端要通过某种标识来进行通信，从而让服务器判断请求资源是否可以缓存访问，这主要涉及到下面两组header字段，这两组搭档都是成对出现的，即第一次请求的响应头带上某个字段（Last-Modified或者Etag），则后续请求则会带上对应的请求字段（If-Modified-Since或者If-None-Match），若响应头没有Last-Modified或者Etag字段，则请求头也不会有对应的字段。 

> **Last-Modify/If-Modify-Since**   

> **[Response Headers]**  
> 响应值：`Last-Modified:Mon, 25 Sep 2017 10:25:16 GMT`

> **[Request Headers]**    
> 响应值：`If-Modified-Since:Thu, 12 Nov 2015 03:53:04 GMT`

> 表示该资源的最后修改时间  
> 当浏览器再次请求该资源时，request的请求头中会包含If-Modify-Since，该值为缓存之前返回的Last-Modify。服务器收到If-Modify-Since后，根据资源的最后修改时间判断是否命中缓存。  
> 如果命中缓存，则返回304，并且不会返回资源内容，并且`不会返回Last-Modify`。  

> **ETag/If-None-Match** `（高优先级）` 

> **[Response Headers]**  
> 响应值：`Etag:e2f5c5e5d8e677f71d35164fab4b7c24`  

> **[Request Headers]**  
> 响应值：`If-None-Match:e2f5c5e5d8e677f71d35164fab4b7c24`  
> 与上组不同，改组返回的ETag是一个校验码，确保每个资源唯一。服务器根据浏览器上送的If-None-Match值来判断是否命中缓存。  
> 与Last-Modified不一样的是，当服务器返回304 Not Modified的响应时，由于ETag重新生成过，response header中还`会把ETag返回`，即使这个ETag跟之前的没有变化。

> **为什么有了`Last-Modified`,还要`ETag`？**  
> 正解：为了解决`Last-Modified`难解决的问题  
> * 一些文件也许会周期性的更改，但是他的内容并不改变(仅仅`改变的修改时间`)，这个时候我们并不希望客户端认为这个文件被修改了，而重新GET；  
> * 某些文件修改非常频繁，比如在`秒以下的时间内`进行修改，(比方说1s内修改了N次)，If-Modified-Since能检查到的粒度是s级的，这种修改无法判断(或者说UNIX记录MTIME只能精确到秒)；  
> * 某些服务器`不能精确的得到文件的最后修改时间`。

> `Last-Modified` 和 `ETag` 可同时存在，服务器优先验证`ETag`,一致的情况下，才会继续比对Last-Modified，最后才决定是否返回304。

> 强缓存 - 缓存读取 - 状态200：from disk cache 与 from memory cache  
> 协商缓存 - 缓存读取 - 状态304：Not Last-Modified

**[常见操作对缓存的影响]**

用户操作 | Expires/Cache-Control | Last-Modified/Etag
--------- | ------------- | -------------
地址栏回车 | 有效 | 有效
页面链接跳转 | 有效 | 有效
新开窗口 | 有效 | 有效
前进回退 | 有效  | 有效 
F5刷新 | 无效 | 有效
Ctrl+F5强制刷新 | 无效 | 无效

### 实际项目中如何解决缓存问题？
1、本地更新了`base.js`文件，但是发到服务器上再次打开页面没有更新？  
2、通用`common.js`放在公司CDN上，修改替换存在缓存如何解决？  
3、......

### 拓展H5离线存储
Tag：`manifest`、`.appcache`、`本地存储（localStorage、sessionStorage、Cookies、IndexedDB、WebSQL）`等等







