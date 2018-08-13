---
title: Flutter正在加载的实现
date: 2018-08-13
tags: [移动端, Flutter]
categories: 
- 移动端
comments: true
---


以登录页面跳转为例，实现接口请求以及正在加载页面效果。

因为web大家都比较熟悉，就以最简单的方式简单描述一下。

flutter可能比较多的点是需要注意的。

### web中的实现

界面的实现主要通过`position`来将正在加载页面显示在登录表单前面。代码大致如下：
```css
.wrap-loading {
    /* 遮盖整页，并添加背景色 */
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background: rgba(255, 255, 255, .5);
    
    /* 居中 */
    display: flex;
    align-items: center;
    justify-content: center;
}
```
逻辑主要是就显示加载页面-请求完成-隐藏加载页面。代码大致如下：
```javascript
function toLogin () {
    $('.wrap.loading').show()
    $.ajax({
        url: url,
        success: function (data) {
            $('.warp.loading').hide()
            if (success) {
                enterHome()
            } else {
                showError()
            }
        }
    })
}
```

### flutter中的实现

首先还是界面的实现。

实现正在加载页肯定是有许多种方式的，其中最佳的方式应该就是通过`FutureBuilder`（初学dart和flutter，组件是最佳实践，代码是个人目前的能力）。

`FutureBuilder`的官方文档在[这里](https://docs.flutter.io/flutter/widgets/FutureBuilder-class.html)，简单来说的话，就是他可以通过`Future`的状态，显示不同的组件，主要用的构造属性也就是`future`和`builder`。

考虑页面的状态，应该包括还未发送请求状态、请求已发送还未返回的状态、以及请求成功的状态。

在`FutureBuilder`中的`builder`回调函数中，参数为`context`和`AsyncSnapshot`，其中`AsyncSnapshot`包含属性`connectionState`可以用于判断链接的当前状态。

代码大致如下：
```dart
// FutureBuilder中的future需要点击登录之后赋值，所以需要StatefulWidget

class LoginScreen extends StatefulWidget {
  @override
    State<StatefulWidget> createState() {
      return LoginScreenState();
    }
}

class LoginScreenState extends State<LoginScreen> {

  Future<Response> _loginFuture;

  _toHomeScreen (context) {
    Navigator.of(context).pushAndRemoveUntil(
      MaterialPageRoute(
        builder: (context) => HomeScreen()
      ), (route) => false);
  }

  _fetchLogin (username, password) {
    // 发送请求，调用setState，使得重新渲染
    setState(() {
      _loginFuture = http.post(
        baseUrl + loginUrl,
        body: {
          username: username,
          password: password
        }
      );
    });
    return _loginFuture;
  }

  // 返回加载，或什么也不显示
  _showLoading (isShow) {
    if (isShow) {
      return Center(
        child: FullScreenLoading(),
      );
    } else {
      return Center();
    }
  }

  @override
    Widget build(BuildContext context) {
      return FutureBuilder(
        // _loginFuture状态改变，就会重新渲染
        future: _loginFuture,
        builder: (context, snapshot) {
          print(snapshot.connectionState);
          // Stack叠加组件，这里相当于上面web代码中position: absolute的作用
          return Stack(
            children: <Widget>[
              Scaffold(
                body: Container(
                  padding: const EdgeInsets.all(20.0),
                  child: Column(
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: <Widget>[
                      LoginForm(_fetchLogin, success: _toHomeScreen),
                    ],
                  )
                ),
                bottomNavigationBar: Container(
                  margin: const EdgeInsets.only(bottom: 20.0),
                  child: BottomLinksBar(),
                )
              ),
              // 根据状态不同，显示不同
              _showLoading(snapshot.connectionState == ConnectionState.waiting)
            ],
          );
        },
      );
    }

}

```

 在dart中，异步编程主要是在`dart:async`中，包括`Future`和`Stream`等类，这次主要用到的是`Future`，`Futrue`是一个很类似于JavaScript中的`Promise`的存在。
 