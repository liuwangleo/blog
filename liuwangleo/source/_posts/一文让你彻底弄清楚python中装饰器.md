---
title: 一文让你彻底弄清楚python中装饰器
date: 2021-03-07 00:19:57
tags:
    - python进阶
    - 装饰器
categories:
    - python
cover: https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2163877514,2696745730&fm=26&gp=0.jpg
---
>面试官：你了解装饰器吗？
XXX：了解
面试官：那你说说吧
XXX：装饰器是…
面试官：你先写一个简单的装饰器，再写一个带参的装饰器
XXX：xxx。。。
面试官：你知道装饰器有哪些应用场景吗
XXX：xxx。。。(思考)

​大家是否在面试过程中，经常会遇到上面的问题？本文就让我们一起学习python中装饰器。

`装饰器(decorator)`可以说是Python的一个神器，它可以在不改变一个函数代码和调用方式的情况下给函数添加新的功能。Python的装饰器同时也是Python学习从入门到精通过程中必需要熟练掌握的知识。今天尝试用浅显的语言解释下Python装饰器的工作原理及如何编写自己的装饰器吧。

----

# 应用场景
我们先来了解一下它有那些`应用场景`
- 附加功能
- 数据的清理或添加:
    - 函数参数类型验证 @require_ints 类似请求前拦截
    - 数据格式转换 将函数返回字典改为 JSON/YAML 类似响应后篡改
    - 为函数提供额外的数据 mock.patch
- 函数注册
    - 在任务中心注册一个任务
    - 注册一个带信号处理器的函数

你可能还是不理解装饰器是啥？没有关系，下面我们详细的解析一下到底如何去实现装饰器。

# 最简单的装饰器
```python
import time
def getTime(func):
    def inner():
        start = time.time()
        func()
        end = time.time()
        print('用时:{}秒'.format(end-start))
    return inner

@getTime
def func1():
    time.sleep(2)
    print("Func1 is running.")

if __name__ == '__main__':
    func1()
```
上面的代码，`func1`函数被`getTime`函数装饰，这个装饰器实现的功能是`统计func1函数执行的时间`，这其实就是一个最简单的装饰器。
如何你还是不太理解，没有关系，我们换种写法

```python
import time
def getTime(func):
    def inner():
        start = time.time()
        func()
        end = time.time()
        print('用时:{}秒'.format(end-start))
    return inner
def func1():
    time.sleep(2)
    print("Func1 is running.")
res = getTime(func1)
print(res)
```
这种写法是不是清晰很多。如果你还是不太理解，也没有关系，接下来我们就看看装饰器的本质。

# 本质上的装饰器
Python的装饰器本质上是一个`嵌套函数`，它接受被装饰的函数(func)作为参数，并返回一个包装过的函数。这样我们可以在不改变被装饰函数的代码的情况下给被装饰函数或程序添加新的功能。

Python的装饰器广泛应用于`引入日志`，`执行函数前预备处理`，`执行函数后清理功能`，`权限校验`(如django中的@login_required和@permission_required装饰器)，`缓存`，`事务处理`、`性能测试`(比如统计一段程序的运行时间)和`插入日志`等应用场景。有了装饰器，我们就可以抽离出大量与函数功能本身无关的代码，增加一个函数的重用性。

试想你写了很多程序，一直运行也没啥问题。有一天老板突然让你统计每个程序都运行了多长时间并比较下运行效率。此时如果你去手动修改每个程序的代码一定会让你抓狂，而且还破坏了那些程序的重用性。聪明的程序员是绝不能干这种蠢事的。此时你可以编写一个`@time_it`的装饰器。如果你想打印出某个函数或程序运行时间，只需在函数前面@一下，是不是很帅?

由于Python装饰器的工作原理主要依赖于`嵌套函数`和`闭包`，所以我们必须先对嵌套函数和闭包有深入的了解。嵌套函数和闭包几乎是Python工作面试必考题哦。闭包相关的讲解请点击: [python之作用域和闭包详解](https://liuwangleo.github.io/2021/03/06/python%E4%B9%8B%E4%BD%9C%E7%94%A8%E5%9F%9F%E4%B8%8E%E9%97%AD%E5%8C%85%E8%AF%A6%E8%A7%A3/)

## 嵌套函数
如果在一个函数的内部还定义了另一个函数(注意: 是定义，不是引用!），这个函数就叫`嵌套函数`。外部的我们叫它`外函数`，内部的我们叫他`内函数`。

我们先来看一个最简单的嵌套函数的例子。我们在`outer函数`里又定义了一`个inner函数`，并调用了它。你注意到了吗? 内函数在自己作用域内查找局部变量失败后，会进一步向上一层作用域里查找。
```python
def outer():
    x = 1
    def inner():
        y = x + 1
        print(y)
    inner()

outer() #输出结果 2
```

如果我们在外函数里不直接调用内函数，而是通过return inner返回一个内函数的引用 这时会发生什么呢? 你将会得到一个内函数对象，而不是运行结果。
```python
def outer():
    x = 1
    def inner():
        y = x + 1
        print(y)
    return inner

outer() # 输出<function outer.<locals>.inner at 0x039248E8>
f1 = outer()
f1() # 输出2
```

上述这个案例比较简单，因为outer和inner函数都是没有参数的。我们现在对上述代码做点改动，加入参数。你可以看到外函数的参数或变量可以很容易传递到内函数。
```python
def outer(x):
    a = x

    def inner(y):
        b = y
        print(a+b)

    return inner

f1 = outer(1) # 返回inner函数对象
f1(10) # 相当于inner(10)。输出11
```

如果上例中外函数的`变量x`换成被`装饰函数对象(func)`，内函数的`变量y`换成`被装饰函数的参数`，我们就可以得到一个通用的装饰器啦。你注意到了吗? 我们在没对func本身做任何修改的情况下，添加了其它功能, 从而实现了对函数的装饰。
```python
def decorator(func):
    def inner(*args, **kwargs):
        add_other_actions()
        return func(*args, **kwargs)
    return inner
```

## 闭包
[python之作用域和闭包详解](https://liuwangleo.github.io/2021/03/06/python%E4%B9%8B%E4%BD%9C%E7%94%A8%E5%9F%9F%E4%B8%8E%E9%97%AD%E5%8C%85%E8%AF%A6%E8%A7%A3/)


# 面试中的装饰器
## 基本的装饰器
面试官往往会给你提一些简单的需求，如返回被装饰函数执行时间，函数的名称等，这时候要学会灵活的转变。
```python
def hint(func):
    def warpper(*args, **kwargs):
        print("{0} is running".format(func.__name__))
        return func(*args, **kwargs)
    return warpper

@hint
def hello():
    print("say hello!")
```

>调用  hello()
执行结果：hello is running / say hello!

值得一提的是被装饰器装饰过的函数看上去名字没变，其实已经变了。当你运行`print(hello.__name__)`后，你会发现它的名字已经悄悄变成了`warpper`，这显然不是我们想要的。这一点也不奇怪，因为外函数返回的是由`warpper函数`和其`外部引用变量`组成的闭包。

![图片描述](https://img-blog.csdnimg.cn/20201211095412663.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTgxOTYx,size_16,color_FFFFFF,t_70)

为了解决这个问题保证装饰过的函数`__name__`属性不变，我们可以使用`functools`模块里的`wraps`方法，先对`func`变量进行`wraps`。下面这段代码可以作为编写一个通用装饰器的示范代码，注意收藏哦。
```python
from functools import wraps

def hint(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print('{} is running'.format(func.__name__))
        return func(*args, **kwargs)
    return wrapper

@hint
def hello():
    print("Hello!")
```

## 带参的装饰器
前面几个装饰器一般是`内外两层嵌套函数`。如果我们需要编写的装饰器本身是带参数的，我们需要编写三层的嵌套函数，其中最外一层用来传递装饰器的参数。现在我们要对@hint装饰器做点改进，使其能通过@hint(coder='liuwangleo')传递参数。该装饰器在函数运行前给出提示的时候还显示函数编写人员的名字。完整代码如下所示:
```python
from functools import wraps

def hint(coder):
    def wrapper(func):
        @wraps(func)
        def inner_wrapper(*args, **kwargs):
            print('{} is running'.format(func.__name__))
            print('Coder: {}'.format(coder))
            return func(*args, **kwargs)
        return inner_wrapper
    return wrapper

@hint(coder="liuwangleo")
def hello():
    print("Hello!")

```

## 多个装饰器
先来一段代码，自行理解一下
```python
def d1(f):
    def inner1(*args, **kwargs):
       print(1)
       f()
       print("我是外边的装饰器")
    return inner1
def d2(f):
    def inner2(*args, **kwargs):
        print(2)
        print(f.__name__)
        f()
        print("我是里边的装饰器")
    return inner2
@d1
@d2
def func():
    print('哈哈哈')
func()
# 执行结果是啥呢？
```

不知道你能否理解，如果不明白没有关系，前面我们介绍过装饰器就是嵌套函数，不妨还是换种写法
```python
def d1(f):
    def inner1(*args, **kwargs):
        print(2)
        f()
        print("我是外边的装饰器")

    return inner1
def d2(f):
    def inner2(*args, **kwargs):
        print(1)
        print(f.__name__)
        f()
        print("我是里边的装饰器")

    return inner2
def func():
    print('哈哈哈')
res = d1(d2(func))
```
这样是不是就容易理解了，执行结果呢？来了来了。。。
```
2
1
func
哈哈哈
我是里边的装饰器
我是外边的装饰器
```

如果上面代码没看够，那就再来一段
```python
from functools import wraps
def A(funE_decorated_by_C):
    @wraps(funE_decorated_by_C)
    def redecorated_E(str):
        print("开始执行", end="——>")
        return funE_decorated_by_C(str) + ' > redecorated by A'
    return redecorated_E
def C(funE):
    @wraps(funE)
    def decorated_E(str):
        return funE(str) + ' > decorated by C'
    return decorated_E
@A
@C
def E(str):
    return str
print(E('A string is '))
print(E.__name__)
```

**接下来不要眨眼，让我们详细的看一下这种多个装饰器的执行过程**

---
`开始执行——> A string is > decorated by C > redecorated by A`

难理解的地方在C和A在装饰过程中执行的调用关系

- 可以看到，`先调用那个装饰器，那个装饰器就先运行`
    print(“开始执行”, end="——>")

- 继续执行 A装饰的是`C(A)` 即调用 `funE_decorated_by_C`
    调用` funE_decorated_by_C` 执行C

- C执行 调用E函数 返回的结果`" A string is > decorated by C "`  作为A中 `funE_decorated_by_C` 的`参数`

- 最终在A中调用E 返回 A

---

最后在附上一份案例代码，供参考
```python
def decorator_b(fun):
    def inner_b(*args, **kwargs):
        print('This is inner_b')
        print("*****")
        # print(fun(*args, **kwargs))
        return fun(*args, **kwargs) + 2

    return inner_b


def decorator_a(fun):
    def inner_a(*args, **kwargs):
        print('This is inner_a')
        return fun(*args, **kwargs) + 11

    return inner_a


@decorator_b
@decorator_a
def f(x):
    print('This is f')
    return x * 2


print(f(1))
```

# 推荐文章
https://www.cnblogs.com/Lin-Yi/p/7305364.html
http://python.jobbole.com/81683/
http://lib.csdn.net/article/python/62942
http://lib.csdn.net/article/python/64769
http://www.cnblogs.com/cicaday/p/python-decorator.html

最新推荐：
    [恶补了 Python 装饰器的六种写法，你随便问~](https://mp.weixin.qq.com/s/XTHZ-K1yDNPIoejNFvk6rw)
    [各种各样的装饰器实现详解](https://blog.csdn.net/LaoYuanPython/article/details/111303395)