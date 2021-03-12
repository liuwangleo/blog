---
title: 傻傻分不清之Cookie、Session、Token、JWT
date: 2021-03-09 10:24:49
tags:
    - cookie、session、token
    - JWT
categories:
    - 后端
---
# 认证(Authentication)
- 通俗地讲就是`验证当前用户的身份`，证明 “你是你自己”（比如：你每天上下班打卡，都需要通过指纹打卡，当你的指纹和系统里录入的指纹相匹配时，就打卡成功）
- 互联网中的认证：
    - 用户名密码登录
    - 邮箱发送登录链接
    - 手机号接收验证码
    - 只要你能收到邮箱 / 验证码，就默认你是账号的主人

# 授权(Authorization)
- 用户授权第三方应用`访问`该用户某些`资源`的`权限`
    - 你在安装手机应用的时候，APP 会询问是否允许授予权限（访问相册、地理位置等权限）
    - 你在访问微信小程序时，当登录时，小程序会询问是否允许授予权限（获取昵称、头像、地区、性别等个人信息）
- 实现授权的方式有：cookie、session、token、OAuth

# 凭证(Credentials)
- 实现认证和授权的前提是需要一种媒介（证书） 来标记访问者的身份
    - 在战国时期，商鞅变法，发明了照身帖。照身帖由官府发放，是一块打磨光滑细密的竹板，上面刻有持有人的头像和籍贯信息。国人必须持有，如若没有就被认为是黑户，或者间谍之类的。
    - 在现实生活中，每个人都会有一张专属的居民身份证，是用于证明持有人身份的一种法定证件。通过身份证，我们可以办理手机卡 / 银行卡 / 个人贷款 / 交通出行等等，这就是认证的凭证。
    - 在互联网应用中，一般网站（如掘金）会有两种模式，游客模式和登录模式。游客模式下，可以正常浏览网站上面的文章，一旦想要点赞 / 收藏 / 分享文章，就需要登录或者注册账号。当用户登录成功后，服务器会给该用户使用的浏览器颁发一个令牌（token），这个令牌用来表明你的身份，每次浏览器发送请求时会带上这个令牌，就可以使用游客模式下无法使用的功能。

# cookie
**HTTP是无状态的协议(对于事务处理没有记忆能力，每次客户端和服务端会话完成时，服务器端不会保存任何会话信息)。**每一个请求都是`完全独立`的，服务端无法确认当前访问者的身份信息，无法分辨上一次的请求发送者和这一次的发送者是不是同一个人。所以服务器与浏览器为了进行`会话跟踪`（知道是谁在访问我），就必须主动的去维护一个状态，这个状态用于告知服务端前后两个请求是否来自同一浏览器。而这个状态需要通过 `cookie `或`session`去实现。

- **cookie存储在客户端**
    `cookie`是`服务器`发送到用户浏览器并保存在`本地`的一块`数据`，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。
- **cookie是不可跨域的**
    每个 cookie 都会绑定单一的域名，无法在别的域名下获取使用，一级域名和二级域名之间是允许共享使用的（靠的是 domain）。

**cookie的重要性**

|属性 |说明 |
|:-----|:----|
|name = value|键值对，设置cookie的名称及相对应的值，都必须是字符串类型|

- 如果值为`Unicode`字符，需要为`字符编码`。
- 如果值为二进制数据，则需要使用 BASE64 编码。
    - `domain`指定`cookie`所属域名，默认是当前域名 | | path | 指定 cookie 在哪个路径（路由）下生效，默认是 '/'。
    - 如果设置为 /abc，则只有 /abc 下的路由可以访问到该 cookie，如：/abc/read。| | maxAge | cookie 失效的时间，单位秒。如果为整数，则该 cookie 在 maxAge 秒后失效。如果为负数，该 cookie 为临时 cookie ，关闭浏览器即失效，浏览器也不会以任何形式保存该 cookie 。如果为 0，表示删除该 cookie 。默认为 -1。
- 比 expires 好用。
    - | | expires | 过期时间，在设置的某个时间点后该 cookie 就会失效。
一般浏览器的 cookie 都是默认储存的，当关闭浏览器结束这个会话的时候，这个`cookie`也就会被删除`secure`该`cookie`是否仅被使用安全协议传输。安全协议有 HTTPS，SSL 等，在网络上传输数据之前先将数据加密。默认为 false。
    - 当 secure 值为 true 时，cookie 在 HTTP 中是无效，在 HTTPS 中才有效。| | httpOnly | 如果给某个 cookie 设置了 httpOnly 属性，则无法通过 JS 脚本 读取到该 cookie 的信息，但还是能通过 Application 中手动修改 cookie，所以只是在一定程度上可以防止 XSS 攻击，不是绝对的安全 

# session
`session`是另外一种记录服务器和客户端会话状态的机制。`session`是基于`cookie`实现的，session存储在服务器端，`sessionid`会被存储在客户端的`cookie`中

**session认证流程**
![session认证流程](https://img-blog.csdnimg.cn/20201230023113228.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTgxOTYx,size_16,color_FFFFFF,t_70)

- 用户第一次请求服务器的时候，服务器根据用户提交的相关信息，创建对应的`session`
- 请求返回时将此`session`的唯一标识信息 `sessionID`返回给浏览器
- 浏览器接收到服务器返回的 SessionID 信息后，会将此信息存入到 Cookie 中，同时 Cookie 记录此 SessionID 属于哪个域名
- 当用户第二次访问服务器的时候，请求会自动判断此域名下是否存在 Cookie 信息，如果存在自动将 Cookie 信息也发送给服务端，服务端会从 Cookie 中获取 SessionID，再根据 SessionID 查找对应的 Session 信息，如果没有找到说明用户没有登录或者登录失效，如果找到 Session 证明用户已经登录可执行后面操作。

**根据以上流程可知，SessionID 是连接 Cookie 和 Session 的一道桥梁，大部分系统也是根据此原理来验证用户登录状态。**

# Cookie和Session的区别
- 安全性
    session比cookie安全，session是存储在服务器端的
    cookie是存储在客户端
- 存取值的类型不同
    cookie只支持字符串数据，想要设置其他类型的数据，需要将其转换为字符串
    session可以存任意数据类型
- 有效期
    cookie可设置为长时间保持，比如我们经常使用的默认登录功能
    session一般失效时间较短，客户端关闭或session超时都会失效
- 存储大小不同
    单个cookie保存的数据不能超过4K
    session可存储数据远高于cookie，但是当访问量过多，会占用过多的服务器资源

# token(令牌)

## Access Token
访问资源接口(API)时所需要的资源令牌

简单token的组成：`uuid`用户唯一的身份标识、`time`当前时间的时间戳、`sign`(签名,token的前几位以哈希算法压缩成的一定长度的十六进制字符串)

**特点**
- 服务端无状态化，可扩展性好
- 支持移动端设备
- 安全
- 支持跨程序调用

**token的身份验证流程**
![token的身份验证流程](https://img-blog.csdnimg.cn/20201230023718475.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTgxOTYx,size_16,color_FFFFFF,t_70)

- 客户端使用用户名跟密码请求登录
- 服务端收到请求，去验证用户名与密码
- 验证成功后，服务端会签发一个 token 并把这个 token 发送给客户端
- 客户端收到 token 以后，会把它存储起来，比如放在 cookie 里或者 localStorage 里    
- 客户端每次向服务端请求资源的时候需要带着服务端签发的 token
- 服务端收到请求，然后去验证客户端请求里面带着的 token ，如果验证成功，就向客户端返回请求的数据
- 每一次请求都需要携带 token，需要把 token 放到 HTTP 的 Header 里

> 基于`token`的用户认证是一种服务端`无状态`的认证方式，服务端不用存放`token`数据。用解析`token`的计算时间换取`session`的存储空间，从而减轻服务器的压力，减少频繁的查询数据库
`token`完全由应用管理，所以它可以`避开同源策略`

## Refresh Token
`refresh token`是专用于刷新`access token`的 `token`。如果没有` refresh token`，也可以刷新 `a`ccess token`，但每次刷新都要用户输入登录用户名与密码，会很麻烦。有了 `refresh token`，可以减少这个麻烦，客户端直接用 refresh token 去更新 access token，无需用户进行额外的操作。

![图片](https://img-blog.csdnimg.cn/20201230024210486.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTgxOTYx,size_16,color_FFFFFF,t_70)

> `Access Token`的有效期比较短，当`Acesss Token`由于过期而失效时，使用`Refresh Token`就可以获取到新的`Token`，如果 `Refresh Token `也失效了，用户就只能重新登录了。
`Refresh Token`及`过期时间`是存储在`服务器`的数据库中，只有在申请新的` Acesss Token` 时才会验证，不会对业务接口响应时间造成影响，也不需要向` Session` 一样一直保持在内存中以应对大量的请求。

# Token和Session的区别
- `Session`是一种记录`服务器`和`客户端`会话状态的机制，使服务端有状态化，可以记录会话信息。而`Token`是令牌，访问资源接口（API）时所需要的资源凭证。Token 使服务端无状态化，不会存储会话信息。

- `Session` 和 `Token `并不矛盾，作为身份认证 `Token `安全性比` Session `好，因为每一个请求都有签名还能防止监听以及重放攻击，而 Session 就必须依赖链路层来保障通讯安全了。如果你需要实现有状态的会话，仍然可以增加 Session 来在服务器端保存一些状态。

- 所谓`Session认证`只是简单的把 User 信息存储到 `Session `里，因为 `SessionID` 的不可预测性，暂且认为是安全的。而 `Token` ，如果指的是 `OAuth Token `或类似的机制的话，提供的是 `认证 `和` 授权` ，认证是针对用户，授权是针对 App 。其目的是让某 App 有权利访问某用户的信息。这里的 Token 是唯一的。不可以转移到其它 App 上，也不可以转到其它用户上。`Session `只提供一种简单的认证，即只要有此 `SessionID` ，即认为有此 User 的全部权利。是需要严格保密的，这个数据应该只保存在站方，不应该共享给其它网站或者第三方 App。所以简单来说：如果你的用户数据可能需要和第三方共享，或者允许第三方调用 API 接口，用 Token 。如果永远只是自己的网站，自己的 App，用什么就无所谓了。

# JWT
## 介绍
`Json Web Token`(简称jwt)是目前最流行的跨域认证解决方案，是一种认证授权机制。JWT是为了在网络应用环境间传递声明而执行的一种基于`JSON`的开放标准。JWT的声明一般被用来在身份提供者和服务提供者传递被认证的用户身份信息，以便于资源服务器获取资源。比如用户的登录。可以使用`HMAC算法`或者是`RSA的公/私秘钥`对`JWT`进行签名。因为数字签名的存在，这些传递的信息是可信的。阮一峰老师的 JSON Web Token 入门教程 讲的非常通俗易懂。

## 生成JWT
www.jwt.io/
www.jsonwebtoken.io/

## JWT的原理
![原理](https://img-blog.csdnimg.cn/20201230024317770.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTgxOTYx,size_16,color_FFFFFF,t_70)

**JWT认证流程**
- 用户输入用户名/密码登录，服务端认证成功后，会返回给客户端一个JWT
- 客户端将token保存到本地(通常使用localstorage或者cookie)
- 当用户希望访问一个受保护的路由或者资源的时候，需要请求头的`Authorization`字段中使用`Bearer`模式添加`JWT`，其内容看起来是下面这样
```
Authorization: Bearer <token>
```

> 服务端的保护路由将会检查请求头 Authorization 中的 JWT 信息，如果合法，则允许用户的行为
因为 JWT 是自包含的（内部包含了一些会话信息），因此减少了需要查询数据库的需要
因为 JWT 并不使用 Cookie 的，所以你可以使用任何域名提供你的 API 服务而不需要担心跨域资源共享问题（CORS）
因为用户的状态不再存储在服务端的内存中，所以这是一种无状态的认证机制

## JWT的使用方式

> 项目：<https://github.com/yjdjiayou/jwt-demo>

**方式一**
当用户希望访问一个受保护的路由或者资源的时候，可以把它放在 Cookie 里面自动发送，但是这样不能跨域，所以更好的做法是放在 HTTP 请求头信息的 Authorization 字段里，使用 Bearer 模式添加 JWT。

```
GET /calendar/v1/events
Host: api.example.com
Authorization: Bearer <token>
```
>- 用户的状态不会存储在服务端的内存中，这是一种 无状态的认证机制
- 服务端的保护路由将会检查请求头 Authorization 中的 JWT 信息，如果合法，则允许用户的行为。
- 由于 JWT 是自包含的，因此减少了需要查询数据库的需要
- JWT 的这些特性使得我们可以完全依赖其无状态的特性提供数据 API 服务，甚至是创建一个下载流服务。
- 因为 JWT 并不使用 Cookie ，所以你可以使用任何域名提供你的 API 服务而不需要担心跨域资源共享问题（CORS）


**方式二**
跨域的时候，可以把 JWT 放在 POST 请求的数据体里。

**方式三**
通过 URL 传输 <http://www.example.com/user?token=xxx>


# Token 和 JWT 的区别
- 相同：
    - 都是访问资源的令牌
    - 都可以记录用户的信息
    - 都是使服务端无状态化
    - 都是只有验证成功后，客户端才能访问服务端上受保护的资源
- 区别：
    - Token：服务端验证客户端发送过来的 Token 时，还需要查询数据库获取用户信息，然后验证 Token 是否有效。
    - JWT：将 Token 和 Payload 加密后存储于客户端，服务端只需要使用密钥解密进行校验（校验也是 JWT 自己实现的）即可，不需要查询或者减少查询数据库，因为 JWT 自包含了用户信息和加密的数据。

# 常见的加密算法
![算法](https://img-blog.csdnimg.cn/20201230024511539.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTgxOTYx,size_16,color_FFFFFF,t_70)

- 哈希算法 (Hash Algorithm) 又称散列算法、散列函数、哈希函数，是一种从任何一种数据中创建小的数字 “指纹” 的方法。哈希算法将数据重新打乱混合，重新创建一个哈希值。
- 哈希算法主要用来保障数据真实性 (即完整性)，即发信人将原始消息和哈希值一起发送，收信人通过相同的哈希函数来校验原始数据是否真实。
- 哈希算法通常有以下几个特点：
    - 2 的 128 次方为 340282366920938463463374607431768211456，也就是 10 的 39 次方级别
    - 2 的 160 次方为 1.4615016373309029182036848327163e+48，也就是 10 的 48 次方级别
    - 2 的 256 次方为 1.1579208923731619542357098500869 × 10 的 77 次方，也就是 10 的 77 次方
    - 正像快速：原始数据可以快速计算出哈希值
    - 逆向困难：通过哈希值基本不可能推导出原始数据
    - 输入敏感：原始数据只要有一点变动，得到的哈希值差别很大
    - 冲突避免：很难找到不同的原始数据得到相同的哈希值，宇宙中原子数大约在 10 的 60 次方到 80 次方之间，所以 2 的 256 次方有足够的空间容纳所有的可能，算法好的情况下冲突碰撞的概率很低：

>**注意：**
以上不能保证数据被恶意篡改，原始数据和哈希值都可能被恶意篡改，要保证不被篡改，可以使用 RSA 公钥私钥方案，再配合哈希值。
哈希算法主要用来防止计算机传输过程中的错误，早期计算机通过前 7 位数据第 8 位奇偶校验码来保障（12.5% 的浪费效率低），对于一段数据或文件，通过哈希算法生成 128bit 或者 256bit 的哈希值，如果校验有问题就要求重传。