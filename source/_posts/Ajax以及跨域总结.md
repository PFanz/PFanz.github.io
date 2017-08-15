---
title: Ajax以及跨域总结
date: 2017-8-15
tags: [JavaScript, 项目经验]
categories:
- JavaScript
comments: true
---

Ajax主要用来前端发送get或post请求，可以实现前端向后端的通信。
```JavaScript
var xhr = new XMLHttpRequest()
xhr.onreadystatechange = function () {
  if (xhr.readyState == 4 && xhr.status == 200) {
    document.getElementById("myDiv").innerHTML = xhr.responseText
  }
}
xhr.open("GET", "test1.txt", true)
xhr.send()
```

什么是跨域？

协议、域名、端口有任何一个不相同，则为跨域。  
协议主要指http和https，域名需要注意包括二级域名，端口一般情况下线上环境都是80端口，不会存在不同情况。

限制跨域请求是浏览器的安全行文，虽然说对浏览器进行一些配置可以允许在该浏览器上的跨域请求，但是这就和出门顺便打开大门一样。



