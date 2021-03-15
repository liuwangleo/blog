title: python之作用域与闭包详解
author: liuwangleo
date: 2021-03-06 19:29:00
tags:
    - python进阶
    - 作用域
    - 闭包
categories:
    - python
cover: https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=2614262165,254938820&fm=26&gp=0.jpg
---
# 作用域介绍
作用域，也叫名称空间。
## 几个相关的概念
- 全局名称空间：创建的存储“变量名与值的关系”的空间叫做全局名称空间
- 局部名称空间：在函数的运行中开辟的临时的空间叫做局部名称空间
- 内置名称空间：内置名称空间中存放了python解释器为我们提供的名字：input,print,str,list,tuple...它们都是我们熟悉的，拿过来就可以用的方法。

## python的作用域
- L：local，局部作用域，即函数中定义的变量；
- E：enclosing，嵌套的父级函数的局部作用域，即包含此函数的上级函数的局部作用域，但不是全局的（闭包常见）；
- G：globa，全局变量，就是模块级别定义的变量；
- B：built-in，系统固定模块里面的变量，比如int, bytearray等。

`加载变量的优先顺序是由下至上`
`搜索变量的优先顺序是由上至下，LEGB法则`

# 作用域产生
在Python中，只有模块（module），类（class）以及函数（def、lambda）才会引入新的作用域，其它的代码块（如if、try、for等）是不会引入新的作用域的，如下代码：

```python
if 2>1:
    x = 1
print(x)  # 1
```
这个是没有问题的，if并没有引入一个新的作用域，x仍处在当前作用域中，后面代码可以使用。

```python
def test():
    x = 2
print(x) # NameError: name 'x2' is not defined
```

# 案例和几个关键字
## 变量的声明和使用

```python
x=2
def f2():
    print(x)
    x=1
f2()
# local variable 'x' referenced before assignment
```
上面的代码执行是会报错的，我们都知道变量是先声明，再引用的，错误的原因在于 print(x)，解释器会在局部作用域找，会找到x=1(函数已经加载到内存),但x使用在声明前了,所以报错；如何证明找到了x=1呢?简单:注释掉x=2,x=1,报错为:`name 'x' is not defined`

```python
x=2
def f2():
    x+=1 #local variable 'x' referenced before assignment.  x 使用之前已经被声明了
#x+=1：x = x + 1；x 已经被声明了，x=2，这里等于 2 = 2 + 1，发生报错
f2()
```
## global关键字
针对上边代码的报错，我们知道在局部作用域里是不能操作全局变量的，我们先看一段代码：

```python
a = 100
def f():
	a = 200
	print(a)
f()
print(a)
# 200
# 100
```
如果要是想修改这个全局变量a的值，在python中有关键字可以实现这个需求—global

```python
# global关键字可以在局部中修改全局的变量
a = 100
def f():
    global a
    a = 200
    print(a)
f()
print(a)
# 200
# 200
```
## nonlocal关键字
```python
# nonlocal关键字可以修改嵌套的父级函数的局部变量的值
def outer():
    count = 10
    def inner():
        nonlocal count
        count = 20
        print(count)
    inner()
    print(count)
outer()
# 20
# 20
```
这段代码是不是看着有点懵比呢。这是因为这个函数使用到了闭包，**inner**函数中使用**nonlocal **修改了count的值，**nonlocal关键字可以修改嵌套的父级函数的局部变量的值**，下面我们了解一下**闭包**

# 闭包
## 特别简单的介绍
 还是先来段代码
```python
def outer():  
    x = 1  
    def inner():  
        print(x) # 1  
    return inner  
foo = outer()  
print(foo)
# <function outer.<locals>.inner at 0x000001EDE42878C8> 
foo()  
# 1
```
**其实这就是闭包**

闭包的三要素：
- 定义两个函数内部函数与外部函数  
- 在外部函数中存在一个自由变量，内部函数使用 
- 外部函数的返回值调用内部函数

> 在你面试的时候回答闭包问题，完全可以直接附上代码，讲出三要素

## 闭包的陷阱
```python
def my_func(*args):
    fs = []
    for i in range(3):
        def func():
            return i * i
        fs.append(func)
    return fs

fs1, fs2, fs3 = my_func()
print(fs1())
print(fs2())
print(fs3())
# 全部返回值为 4
# 返回闭包中不要引用任何循环变量，或者后续会发生变化的变量。
# 这条规则本质上是在返回闭包前，闭包中引用的父函数中定义变量的值可能会发生不是我们期望的变化。
```
```python
def my_func(*args):
    fs = []
    for i in range(3):
        func = lambda _i = i : _i * _i
        fs.append(func)
return fs
# 这个函数返回什么呢？思考一下，可以留言回复
```
其实就这些东西，一次性弄清楚之后，再遇到相关的问题就不会概念不清，思路混乱。讲到闭包其实**装饰器**也不得不说，装饰器基本面试或者工作用的比较多，之后会专门更一篇相关的博文。
[**在这里：专题九:如何应对面试官的拷问—你了解python的装饰器吗？**](https://blog.csdn.net/qq_36581961/article/details/111028518)

**谢谢观看，点赞关注不迷路，我很菜还不爱说！**