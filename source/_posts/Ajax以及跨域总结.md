---
title: Ajax以及跨域总结
date: 2017-8-16
tags: [JavaScript, 项目经验]
categories:
- JavaScript
comments: true
---

Ajax主要用来前端发送get或post请求，可以实现前端向后端的通信。  
Ajax是通过XMLHttpRequest实现的，只需要四步。  
第一步创建XMLHttpRequest对象。(兼容IE的判断是可以不再考虑)  
第二步监听readystatechange事件，在请求发送到服务器期间，会触发一系列的readystatechange事件，当readyState===4的时候，表示请求已经完成，响应已经处理完毕。  
第三步定义请求，可以定义请求方式，请求地址，是否异步。  
第四部发送请求。
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
限制跨域请求是浏览器的安全行为。

实现跨域的方式

第一种是JSONP

虽然在jQuery中，JSONP的使用方式也是$.ajax，但是JSONP并不属于Ajax，因为其并不是通过XMLHttpRequest来实现的。  
我们在使用script标签的时候可以引用各种域名下的资源，不会有跨域限制，并且script中引用的代码会在加载结束以后执行。
```html
// index.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>JSONP</title>
</head>
<body>
  <script type="text/javascript">
    var scriptElem = document.createElement('script')
    scriptElem.src = './index.js'
    document.head.append(scriptElem)
  </script>
</body>
</html>

// index.js
cb = {
  a: 'a'
}
```
上面的代码会在全局引入变量cb，通过cb就可以拿到对象{a: 'a'}，这就是JSONP的原理了。 
在实际使用中，一般情况是jsonp请求是在普通请求后面添加参数callback，服务器端根据是否包含callback决定返回json格式还是"jsonp"格式。前端在请求前定义与callback值同名的方法，服务器端返回"jsonp"为该方法的执行，参数就是我们需要的数据。  
如果使用jQuery等库都会封装前端这些操作，只需要后端对callback进行判断。  


第二种是跨域资源共享（CORS）

这种方法更为简单，不需要前端做任何工作，其实现就是服务器端在返回结果的时候告诉浏览器这个资源是允许被跨域请求的。通过http的头信息中的`Access-Control-Allow-Origin`的值，可以实现资源的跨域，如果值设置为*表示允许所有的地址跨域请求该资源。

ps: 在实际开发中还遇到过css引用字体文件跨域问题，解决办法同样是使用CORS。如果字体使用不是特别多，也可以使用字蛛进一步压缩字体文件后，使用base64来解决跨域问题。


更多关于跨域问题可以参考阮一峰老师的[这个](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)以及[这个](http://www.ruanyifeng.com/blog/2016/04/cors.html)



