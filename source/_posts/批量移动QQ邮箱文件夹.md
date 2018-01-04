---
title: 批量移动QQ邮箱文件夹
date: 2017-03-14
tags: JavaScript
categories:
- JavaScript
comments: true
---

在使用QQ企业邮箱中，设置了错误的收件规则，导致收件箱所有邮件移动到了自定义文件夹中。手动去移回这些邮件，没有找到响应的批量处理，一次只能移动一页，这样下来有170+页，是个麻烦事。

第一个想到的方法是查看API，发现每次移动调用的参数是这样的
```
&location=mail_list

&mailid=ZC0513-BHmnKHGM8D_6qBtDH4Lae7f
&mailid=ZL0513-ss~T6rNM2RbTpRZO~VGRf7f
... ...
&mailid=ZL0514-l~qc1YzCN_g_zX4mtbd3B7f

&mailaction=mail_move

&destfolderid=1

&t=mail_mgr2

&resp_charset=UTF8

&ef=js

&sid=-IELu_LevHO0lyR8,2
```
试着调用一次，发现失败了。为了节约时间，采用了模拟点击的方法来实现批量移动。

我们要实现的功能挺简单的，点击全选按钮，触发移动到里面的收件箱按钮。  
QQ邮箱网页版是多个iframe嵌套实现的，也正是因为这样的iframe嵌套结构，使我们能够在邮件再次加载的时候我们的js代码还存在环境中，(这样是不是意味着对某个网页进行批量操作时，操作如果会刷新页面，可以使用iframe嵌套来创造程序运行的环境呢？)我们用的到主要是邮件这个ifame。  
iframe的操作主要包括一个top，一个parent。每个iframe相当于一个window对象，通过iframe的name可以访问响应的iframe。在chrome浏览器的console中是可以切换当前代码在哪个iframe下执行的。![iframe](/images/iframe.png)  
我们需要操作iframe位于mainFrame中，如果将执行代码放在mainFrame中
```JavaScript
function move () {
    window.getTop().selectAll(true,document);getTop().checkAll('mailid',document);
    document.querySelectorAll('#selmContainer')[1].querySelector('.txtflow').click()
    document.querySelector('[title=' + folder + ']').click()
  }
```
这样在每次移动邮件后，mainFrame重新加载，move函数就会丢失，我们无法使用循环来实现。所以可以将执行代码放在top中，所有的window、document都需要指定为mainFrame下的window和document。
```JavaScript

function moveMail (folder, pages) {
  function move () {
    mainFrame.window.getTop().selectAll(true,mainFrame.document);getTop().checkAll('mailid',mainFrame.document);
    mainFrame.document.querySelectorAll('#selmContainer')[1].querySelector('.txtflow').click()
    mainFrame.document.querySelector('[title=' + folder + ']').click()
  }

  for(var i = 0; i < pages; i++) {
    setTimeout(move, (i + 1) * 3000);
  }
}
```
ps:  mainFrame.window === mainFrame
