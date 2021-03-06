---
title: python之迭代器、生成器、推导式
date: 2021-03-04 23:49:33
tags:
    - python进阶
    - 迭代器
    - 生成器
    - 推导式
categories:
    - python
---

# 迭代器
python中的基础数据类型都可以进行循环遍历，通常这种对象称为`可迭代对象`。下面我们回顾一下常用的可迭代对象，`set list tuple dict set`。那为什么我们称他们为可迭代对象呢?因为他们都遵循了可迭代协议,那什么又是可迭代协议呢.首先我们先看一段代码:
```python
s = 'abc'
for i in s:
    print(i)
#结果:
#a b c
#错误的代码:
for i in 123:
    print(i)
#结果
#Traceback (most recent call last):
#  File "D:/python_object/二分法.py", line 62, in <module>
#    for i in 123:
#TypeError: 'int' object is not iterable
```
**注意看报错信息,报错信息中有这样一句话: `‘int’ object is not iterable `翻译过来就是整数类型对象是不可迭代的.**
`iterable`表示可迭代的。那么如何进行验证你的数据类型是否符合`可迭代协议`.我们可以通过`dir`函数来查看类中定义好的所有方法
```python
a = '123'
dir(a)
# # 在打印结果中寻找__iter__ 如果存在就表示当前的这个类型是个可迭代对象
```
这是查看一个对象是否是可迭代对象的第一种方法，我们还可以通过`isinstence()`函数来查看一个对象是什么类型的
```python
l = [1,2,3]
l_iter = l.__iter__()
from collections import Iterable
from collections import Iterator
print(isinstance(l,Iterable)) #True             #查看是不是可迭代对象
print(isinstance(l,Iterator)) #False            #查看是不是迭代器
print(isinstance(l_iter,Iterator)) #True       
print(isinstance(l_iter,Iterable)) #True
```
这里的`__iter__`是帮助我们获取到对象的迭代器.我们使用迭代器中的`__next__()`来获取到一个迭代器的元素,那么我们之前所讲的`for循环机制`
```python
s = "我爱北京天安⻔"
c = s.__iter__() # 获取迭代器
print(c.__next__()) # 使⽤迭代器进⾏迭代. 获取⼀个元素 我
print(c.__next__()) # 爱
print(c.__next__()) # 北
print(c.__next__()) # 京
print(c.__next__()) # 天
print(c.__next__()) # 安
print(c.__next__()) # ⻔
print(c.__next__()) # StopIteration
```
我们使用while循环和迭代器来模拟for循环: 必须要会
```python
lslst = [6,5,4]
l = lst.__iter__()
while True:
    try:
        i = l.__next__()
        print(i)
    except StopIteration
        break
```
**注意: 迭代器不能反复,只能向下执行,并且是一次性的.获取过了就不能在获取了**

# 生成器
`生成器`的本质就是`迭代器`，在python中几种方式来获取生成器
- 通过生成器函数
- 通过各种推导式实现生成器

## 定义生成器
```python
def func():    
    print(11)    
    yield 22
ret = func()
print(ret)
# 运行结果:<generator object func at 0x000001A575163888>
```
回想下迭代器是怎么使用的,再想想生成器的本质就是迭代器.我们是不是就可以直接使用迭代器的方式直接使用生成器
```python
def func():     
    print("111")     
    yield 222
gener = func() 
# 这个时候函数不会执⾏. ⽽是获取到⽣成器
ret = gener.__next__()
# 这个时候函数才会执⾏. yield的作⽤和return⼀样. 也是返回数据
print(ret)
#结果:111222
```
那么我们可以看到,yield和return的效果是一样的,但是还是有点区别
- `yield`是分段来执行一个函数,`yield`可以出现多次
- return是直接停止这个函数,return可以出现多次但是只会执行到第一个就结束了

```python
def func():    
	print("111")    
    yield 222    
    print("333")   
    yield 444
gener = func()
ret = gener.__next__()
print(ret)
ret2 = gener.__next__()
print(ret2)
ret3 = gener.__next__()
# 最后⼀个yield执⾏完毕. 再次__next__()程序报错
print(ret3)
#结果:111222333444
```

## 生成器作用

**生成器的好处是节省内存**

# 推导式
## 列表推导式
列表推导式,生成器表达式以及其他推导式,首先我们先看一下这样的代码,给出一个列表,通过循环,想列表中添加1~10:
```python
li = []
for i in range(10):    
    li.append(i)
print(li)
```
我们换成列表推导式是什么样的,来看看:列表推导式的常⽤写法:
**[结果 for 变量 in 可迭代对象]**
```python
ls = [i for i in range(10)]
print(ls)
```
列表推导式是通过⼀行来构建你要的列表, 列表推导式看起来代码简单. 但是出现错误之后很难排查.
```python
#例. 从python1期到python18期写入列表lst:

lst = ['python%s' % i for i in range(1,19)]
print(lst)

lst = [i for i in range(100) if i %2 == 0]
print(lst)
```

## 生成器推导式
生成器表达式和列表推导式的语法基本上一样的,只是把[]换成()
```python
gen = (i for i in range(10))
print(gen)
# 结果: <generator object <genexpr> at 0x0000026046CAEBF8>
```
打印的结果就是一个生成器,我们可以使用for循环来循环这个生成器
```python
gen = ("第%s次" % i for i in range(10))
for i in gen:    
	print(i)

# 获取1-100内能被3整除的数
gen = (i for i in range(1,100) if i % 3 == 0)
for num in gen:
    print(num)
# 100以内能被3整除的数的平⽅
gen = (i * i for i in range(100) if i % 3 == 0)
for num in gen:
    print(num)
# 寻找名字中带有两个e的人的名字
names = [['Tom', 'Billy', 'Jefferson', 'Andrew', 'Wesley', 'Steven', 'Joe'],
         ['Alice', 'Jill', 'Ana', 'Wendy', 'Jennifer', 'Sherry', 'Eva']]
# 不用推导式和表达式
result = []
for first in names:
    for name in first:
        if name.count("e") >= 2:
            result.append(name)
print(result)
# 推导式
gen = (name for first in names for name in first if name.count('e') >= 2)
for i in gen:
    print(i)
```
**生成器表达式和列表推导式的区别:**
- 列表推导式比较耗内存,一次性加载.生成器表达式几乎不占用内存.使用的时候才分配和使用内存
- 得到的值不一样,列表推导式得到的是一个列表.生成器表达式获取的是一个

## 字典推导式
根据名字应该也能猜到,推到出来的是字典
```python
lst1 = ['jay','jj','meet']
lst2 = ['周杰伦','林俊杰','郭宝元']
dic = {lst1[i]:lst2[i] for i in range(len(lst1))}
print(dic)
```

## 集合推导式
集合推导式可以帮我们直接生成一个集合,集合的特点;无序,不重复 所以集合推导式自带去重功能
```python
lst = [1,2,3,-1,-3,-7,9]
s = {abs(i) for i in lst}
print(s)
```