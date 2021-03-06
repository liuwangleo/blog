---
title: python之赋值、浅拷贝和深拷贝
date: 2021-03-04 09:15:26
tags:
    - python进阶
    - 深浅拷贝
categories:
    - python
---
>**注意：** 结合实际的案例 理解概念

# 赋值
在python中，变量的赋值实际就是简单的`对象引用`，在创建一个对象后，将这个对象赋值给另外一个变量时，python并没有拷贝这个对象，只是拷贝这个对象的引用。

```python
# 定义列表alist
alist = [1, 2, 3]
# 赋值于变量blist
blist = alist
# 查看alist与blist的内存地址  
print('alist id = ', id(alist))
print('blist id = ', id(blist))
#alist和blist的内存地址是一样的
# alist id = 1337323184776
# blist id = 1337323184776

# 任何修改alist或blist的值，再分别读取
print('修改alist的值后')
alist.append('a_append')
print(alist)
print(blist)
# [1,2,3,'a_append']
# [1,2,3,'a_append']
print('修改blist的值后')
blist.append('b_append')
print(alist)
print(blist)
# [1,2,3,'b_append']
# [1,2,3,'b_append']
# 修改任意一个list 两者的都发生变化   alist == blist

```
# 浅拷贝
通过`copy`模块里面的浅拷贝函数`copy()`，对原始变量指向的对象进行浅拷贝，然后浅拷贝生成的新对象赋值给新的变量
浅拷贝会创建一个对象，对于对象中的元素，浅拷贝就只会使用原始元素的引用（内存地址）

**对内部元素影响**
- 不可变类型：浅拷贝只是地址指向，不会开辟新空间
- 可变的数据类型：浅拷贝会开辟新的空间地址(仅仅是顶层开辟了新的空间，里层的元素地址还是一样的)，进行浅拷贝
- 浅拷贝后，改变原始对象中可变类型元素的值，会同时影响拷贝对象，改变原始对象中不可变类型元素的值，只有原始类型受影；操作拷贝对象对原始对象的也是同理

```python
import copy
#定义alist，并拷贝给blist
alist = [1,2,3,[6,7,8]]
blist = alist.copy()

#查看alist 与 blist内存地址
print('alist与blist的地址分别为')
print(id(alist))
print(id(blist))
# alist与blist的地址分别为
# 2056373019592
# 2056373060616

#查看alist与blist内元素的地址
print('alist与blist内元素的地址分别为')
print([id(x) for x in alist])
print([id(x) for x in blist])
# alist与blist内元素的地址分别为
# [140710155244576, 140710155244608, 140710155244640, 2056373019336]
# [140710155244576, 140710155244608, 140710155244640, 2056373019336]

print('修改alist的内元素的值')
alist.append('a_append')
print(alist)
print(blist)
# 修改alist的内元素的值
# [1, 2, 3, [6, 7, 8], 'a_append']
# [1, 2, 3, [6, 7, 8]]

print('修改alist中的列表元素的值')
alist[3].append('a_second_append')
print(alist)
print(blist)
#修改alist中的列表元素的值
# [1, 2, 3, [6, 7, 8, 'a_second_append'], 'a_append']
# [1, 2, 3, [6, 7, 8, 'a_second_append']]

```

# 深拷贝
通过`copy`模块里面的深拷贝函数`deepcopy()`，对原始变量指向的对象进行深拷贝，然后深拷贝生成的新对象赋值给新的变量.跟浅拷贝类似，深拷贝也会创建一个新的对象

> 对于对象中的元素，深拷贝都会重新生成一份，而不是简单的使用原始元素的引用（内存地址）
经过深拷贝后，原始对象和拷贝对象所有的子元素地址都是独立的了

```python
import copy

#定义alist，并深度拷贝给blist
alist = [1,2,3,[6,7,8]]
blist = copy.deepcopy(alist)

#查看alist 与 blist内存地址
print('alist与blist的地址分别为')
print(id(alist))
print(id(blist))
# alist与blist的地址分别为
# 2612203103176
# 2612203144200

#查看alist与blist内元素的地址
print('alist与blist内元素的地址分别为')
print([id(x) for x in alist])
print([id(x) for x in blist])
# alist与blist内元素的地址分别为
# [140710155244576, 140710155244608, 140710155244640, 1993019826888]
# [140710155244576, 140710155244608, 140710155244640, 1993020930824]

print('修改alist的内元素的值')
alist.append('a_append')
print(alist)
print(blist)
# 修改alist的内元素的值
# [1, 2, 3, [6, 7, 8], 'a_append']
# [1, 2, 3, [6, 7, 8]]

print('修改alist中的列表元素的值')
alist[3].append('a_second_append')
print(alist)
print(blist)
# 修改alist中的列表元素的值
# [1, 2, 3, [6, 7, 8, 'a_second_append'], 'a_append']
# [1, 2, 3, [6, 7, 8]]
```

**最后附图一张，便于理解：** [点击查看图源...](https://blog.csdn.net/colourful_sky/article/details/81263998?utm_medium=distribute.pc_relevant.none-task-blog-OPENSEARCH-1.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-OPENSEARCH-1.not_use_machine_learn_pai)

![图片描述](https://img-blog.csdnimg.cn/20201210091158136.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTgxOTYx,size_16,color_FFFFFF,t_70)
