---
title: 初次selenium
date: 2018-06-21
tags: [JavaScript]
categories:
- JavaScript
comments: true
---

selenium是一个浏览器自动化的工具，通过编程可以实现对浏览器的自动控制，常常被用于自动化测试、爬虫等等。因为selenium支持多种语言，所以选择自己常用的语言，很容易就可以上手。

在是用selenium之前，需要安装`webdriver`，并将其加入到环境变量中。不同版本的浏览器，对应不同的webdriver，所以在下载之前，需要先查看安装的浏览器是什么版本。

我按照我的习惯，选择了JavaScript和chrome，[这里](http://seleniumhq.github.io/selenium/docs/api/javascript/index.html)是对应的API文档。

既然要控制浏览器，首先肯定需要一个控制浏览器对象，在这里被称为`WebDriver`，我们更常用的是`ThenableWebDriver`。因为我们操作是需要等待浏览器执行的，所以大部分都是异步操作，而`ThenableWebDriver`，使得我们不必每一步都去调用then，然后在then的回调中执行。  
文档中是这样写的：

```javascript
// A thenable wrapper around a IWebDriver instance that allows commands to be issued directly instead of having to repeatedly call then:

let driver = new Builder().build();
driver.then(d => d.get(url));  // You can do this...
driver.get(url);               // ...or this
```

上面代码中，我们通过`Builder`对象来生成一个`ThenableWebDriver`实例，我理解的是这样`Builder`可以帮我们直接处理好一些配置和环境变量，比如我们想要使用chrome，只需要执行`new Builder().forBrowser('chrome').build()`就可以了。所以我们第一步要做的，一般都是通过`Builder`来生成一个`ThenableWebDriver`实例。

我们可以从API文档中看到`ThenableWebDriver`的所有实例方法，其中`get`方法未打开某个链接，之后我们可以通过`findElement`和`By`对象中的静态方法返回某个元素的`WebElement`对象实例，通过`WebElement`实例的`click`、`sendKeys`等方法执行某些操作，等等。

另外，还有`ThenableWebDriver`的实例方法`wait`也是比较常用的，比如登录跳转，可以通过等待URL变成其他值，再进行接下来的操作。这样可以调用`lib/until`中的静态方法`urlContains`来进行判断。

```javascript
// 这里是个简单的签到功能
(async function () {
  // 配置我们要启动的chrome，这里主要是配置成headless模式（无界面），这样也可以用于Linux
  const chromeOption = new Options()
  chromeOption.headless().windowSize({width: 800, height: 600})
  // 之前没有添加这个参数一直报错
  chromeOption.addArguments('--no-sandbox')
  // 获得ThenableWebDriver实例
  const driver = await new Builder().forBrowser('chrome').setChromeOptions(chromeOption).build()
  try {
    // 打开签到的网页
    await driver.get('https://www.cordcloud.me/auth/login')
    // 找到相应的元素，并通过sendKeys输入用户名，密码，点击登录
    await driver.findElement(By.id('email')).sendKeys('username')
    await driver.findElement(By.id('passwd')).sendKeys('password')
    await driver.findElement(By.id('login')).click()
    // 等待跳转，跳转后的新地址包含user，也可以用完整的url进行判断
    await driver.wait(until.urlContains('user'))
    // 签到按钮，点击
    const btn = await driver.findElement(By.id('checkin'))
    btn.click()
  } catch (error) {
    console.log(error)
  }
})()
```

上面只是最简单的selenium的一个应用，还可以使用`WebElement`实例方法`getText`进行简单的爬虫，可以配合`node-schedule`进行每日定时任务，配合`foreve`守护进程等等。
