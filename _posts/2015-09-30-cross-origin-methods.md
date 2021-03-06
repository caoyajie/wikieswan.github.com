---
layout: post
title: "浏览器跨域问题总结"
description: ""
category: 
tags: []
---
{% include JB/setup %}


# 浏览器跨域问题总结

## 什么是跨域？
概念：只要协议、域名、端口有任何一个不同，都被当作是不同的域。

URL                      	说明       				是否允许通信
http://www.a.com/a.js
http://www.a.com/b.js     	同一域名下   				允许
http://www.a.com/lab/a.js
http://www.a.com/script/b.js 同一域名下不同文件夹 		允许
http://www.a.com:8000/a.js
http://www.a.com/b.js     	同一域名，不同端口  		不允许
http://www.a.com/a.js
https://www.a.com/b.js 		同一域名，不同协议 			不允许
http://www.a.com/a.js
http://70.32.92.74/b.js 	域名和域名对应ip 			不允许
http://www.a.com/a.js
http://script.a.com/b.js 	主域相同，子域不同 			不允许
http://www.a.com/a.js
http://a.com/b.js 			同一域名，不同二级域名（同上） 不允许（cookie这种情况下也不允许访问）
http://www.cnblogs.com/a.js
http://www.a.com/b.js 		不同域名 					不允许

解决方案：

## 跨域资源共享（CORS）

CORS（Cross-Origin Resource Sharing）跨域资源共享，定义了必须在访问跨域资源时，浏览器与服务器应该如何沟通。CORS背后的基本思想就是使用自定义的HTTP头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功还是失败。
服务器端对于CORS的支持，主要就是通过设置Access-Control-Allow-Origin来进行的。如果浏览器检测到相应的设置，就可以允许Ajax进行跨域的访问。
只需要在服务器端设置，客户端普通的js代码就ok。 如何设置，参考 [http://enable-cors.org/server.html](http://enable-cors.org/server.html)

优点 

JSONP只能实现GET请求，而CORS支持所有类型的HTTP请求。

使用CORS，开发者可以使用普通的XMLHttpRequest发起请求和获得数据，比起JSONP有更好的错误处理。

支持cookie

缺点

兼容性 ： JSONP主要被老的浏览器支持，它们往往不支持CORS，而绝大多数现代浏览器都已经支持了CORS（这部分会在后文浏览器支持部分介绍）。
具体参考 https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS

## 通过jsonp跨域

JSONP也叫填充式JSON，是应用JSON的一种新方法，只不过是被包含在函数调用中的JSON，例如：

callback({"name","trigkit4"});
JSONP由两部分组成：回调函数和数据。回调函数是当响应到来时应该在页面中调用的函数，而数据就是传入回调函数中的JSON数据。

JSONP的优缺点

JSONP的优点是：它不像XMLHttpRequest对象实现的Ajax请求那样受到同源策略的限制；它的兼容性更好，在更加古老的浏览器中都可以运行，不需要XMLHttpRequest或ActiveX的支持；并且在请求完毕后可以通过调用callback的方式回传结果。

JSONP的缺点则是：它只支持GET请求而不支持POST等其它类型的HTTP请求；它只支持跨域HTTP请求这种情况，不能解决不同域的两个页面之间如何进行JavaScript调用的问题。

## 通过修改document.domain来跨子域

修改document.domain的方法只适用于不同子域的框架间的交互。

https://developer.mozilla.org/en-US/docs/Web/API/Document/domain

优点 不需要改动服务器端代码，就可以完成跨域自然解决

缺点 仅限二级域名不一样的情况 兼容性


## 本地存储 Cross-origin data storage access

Access to data stored in the browser such as localStorage and IndexedDB are separated by origin. Each origin gets its own separate storage, and JavaScript in one origin cannot read from or write to the storage belonging to another origin.

使用localStorage等浏览器本地存储进行跨域数据传递

优点 hack精神
缺点 代码可读性 兼容性

## 跨域标签API Cross-origin script API access

JavaScript APIs such as iframe.contentWindow, window.parent, window.open and window.opener allow documents to directly reference each other. When the two documents do not have the same origin, these references provide very limited access to Window and Location objects, as described in the next two sections.

优点 比较老的解决方案，稳定，兼容性ok
缺点 代码啰嗦

## window.postMessage

window.postMessage(message,targetOrigin) 方法是html5新引进的特性，可以使用它来向其它的window对象发送消息，无论这个window对象是属于同源或不同源，目前IE8+、FireFox、Chrome、Opera等浏览器都已经支持window.postMessage方法。

优点 跨域页面间自由传递消息
缺点 兼容性





参考

http://blog.smdcn.net/article/1386.html

http://segmentfault.com/a/1190000000718840

https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS

https://developer.mozilla.org/en-US/docs/Web/API/Document/domain

https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy

