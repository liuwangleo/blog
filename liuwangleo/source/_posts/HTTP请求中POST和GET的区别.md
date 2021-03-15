---
title: HTTP请求中POST和GET的区别
date: 2021-03-15 13:14:52
tags:
    - POST与GET
    - HTTP
categories:
    - 计算机网络
cover: https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.taopic.com%2Fuploads%2Fallimg%2F130513%2F240422-1305130F30216.jpg&refer=http%3A%2F%2Fimg.taopic.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1618374397&t=0c801f642fbc728c55bce6c4bd956603
---
# 原理区别
一般我们在浏览器输入一个网址访问网站都是`GET`请求;再`FORM`表单中，可以通过设置`Method`指定提交方式为`GET`或者`POST`提交方式，默认为GET提交方式。

HTTP定义了与服务器交互的不同方法，其中最基本的四种：GET，POST，PUT，DELETE，HEAD，其中`GET`和`HEAD`被称为`安全方法`，因为使用GET和HEAD的HTTP请求不会产生什么动作。不会产生动作意味着GET和HEAD的HTTP请求不会在服务器上产生任何结果。但是安全方法并不是什么动作都不产生，这里的安全方法仅仅指不会修改信息。

根据HTTP规范，`POS`T可能会`修改`服务器上的`资源`的请求。比如CSDN的博客，用户提交一篇文章或者一个读者提交评论是通过POST请求来实现的，因为再提交文章或者评论提交后资源（即某个页面）不同了，或者说资源被修改了，这些便是`“不安全方法”`。

# 两者对比
方法              | GET             | POST
:----------- | :-----------: | :-----------:
||GET在浏览器回退时是无害的    |     POST会再次提交请求
||GET产生的URL地址可以被Bookmark     |POST不可以。 
||GET请求会被浏览器主动cache    |POST不会，除非手动设置。 
||GET请求只能进行url编码，而POST支持多种编码方式。
||GET请求参数会被完整保留在浏览器历史记录里    |POST中的参数不会被保留。
||GET请求在URL中传送的参数是有长度限制的 |POST么有。 
||对参数的数据类型，GET只接受ASCII字符  |  而POST没有限制。 
||GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。 
||GET参数通过URL传  |POST放在Request body中。

# 注意重点
**GET和POST还有一个重大区别：**
简单的说：GET产生一个TCP数据包；POST产生两个TCP数据包。

长的说：对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；
而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）。

也就是说，GET只需要汽车跑一趟就把货送到了，而POST得跑两趟，第一趟，先去和服务器打个招呼“嗨，我等下要送一批货来，你们打开门迎接我”，然后再回头把货送过去。

因为POST需要两步，时间上消耗的要多一点，看起来GET比POST更有效。因此Yahoo团队有推荐用GET替换POST来优化网站性能。