---
title: python基础篇—语法
date: 2021-03-03 00:05:37
tags:
    - python
    - 基础语法
categories:
    - python
cover: https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=1900798621,479273082&fm=26&gp=0.jpg
toc: true
---

# 整形
整形就是十进制整数的统称。<https://blog.csdn.net/qq_36581961/article/details/110467300>
```python
number = 99
print(type(number)) # 查看类型
```

# 字符串
## 定义
字符串就是平时用来表示文本信息。<https://blog.csdn.net/qq_36581961/article/details/110517151>
```python
s1 = '我是字符串-单引号'
s2 = "我是字符串-双引号"
s3 = '''我是用来多行或者换行表示字符串-单引号'''
s4 = """我是用来多行或者换行表示字符串-双引号"""
```
## 常见方法
```python
# 判断字符串以xx开头 返回布尔值
s = "tmd 烦死了"
result = s.startwith('tmd')

# 判断字符串以xx结尾 返回布尔值
s = '生命最将会结束'
result = s.endwith('结束')

# 判断字符串是否为十进制数字 返回布尔值
s = '111'
result = s.isdecimal()

# 去除空格
s = '  我的是liuwangleo   '
s.strip()

# 字符串大小写转换
s = 'Hello World LEO'
su = s.upper()
sl = su.lower()

# 内容替换
s = '祝你幸福，你要好好的'
s.replace('你'，'我')

# 字符出切割
s = '1,2,3,4,5'
s.split(',')  # [1,2,3,4,5]
# 注意 加第二参数时 
s.split(',',2)  # 分割次数 默认为-1

# 字符串拼接
l = [1,2,3,4,5]
'-'.join(l)

# 格式化字符串
# %s
# format
s1 = "我喜欢{0}、{1}".format('python', '中国')
s2 = "我喜欢%s、%s" % ('python', '中国')
```

## 其他
```python
# 转换
s = str(1111)
type(s)

# 获取长度
s = '我有多长'
len(s)

"""
这部分建议去shell里测试一下
"""
# 索引 重要 *
s = '1234,木头人@leo'
s[0]
s[len(s)-1]
s[-1]

# 切片
s = 'liuwangleo.github.io'
s[0:]
s[1:4]
s[:-1]
s[-2:-1]

# 步长 
s = "生活不是电影，生活比电影苦"
s[::2]
s[2::2]
s[::-1]  #倒序
s[1:3:-1]  # 报错
s[3:1:-1]  # '是不'
```

# 列表

## 定义
可以把列表当做是一个有序的容器，在里面可以放很多的元素。<https://blog.csdn.net/qq_36581961/article/details/110517411>
```python
num_list = [1,2,3,4]
```

## 常用方法
```python
# 追加 
data = [1,2,3,4]
data.append('字符串混入')

# 插入
data = [1,'22','a']
data.insert(0,'0a')

# 扩展
data = ['嗯','哦']
data.extend(['啊','哈'])

# 根据值移除
data = [1,2,3,4,'q']
data.remove('q')
data.remove('q') # 会报错

# 根据索引删除
data = [1,2,3,4,'q']
data.pop()
data.pop(1) # 删除索引未2的值 倒数第二个值

# 清空列表
data = [1,2,3,4,'q']
data.clear()

# 反转列表
data = [1,2,3,4,'q']
data.reverse()
```

## 其他
```python
# 获取长度
data = [1,2,3,4,'q']
len(data)

# 索引
data = [1,2,3,4,'q']
data[0]
data[-2]

# 修改元素
data = [1,2,3,4,'q']
data[-1] = 5

# 切片
data = [1,2,3,4,'q']
data[0:2]
data[:-1]

# 步长
data = [1,2,3,4,'q']
data[0::2]
```

# 元组

## 定义
元组也是一个容器，里面可以存放各种数据（和列表相似），但他有一个特殊的特点：元组的儿子元素不允许添加、不允许修改、不允许删除，只能读。<https://blog.csdn.net/qq_36581961/article/details/110621604>

```python
t1 = (1,)
t3 = (1,2)
t3 = (1,2,3,)
"""
# 面试题
1. 比较值 v1 = (1) 和 v2 = 1 和 v3 = (1,) 有什么区别？
2. 比较值 v1 = ((1),(2),(3)) 和 v2 = ((1,),(2,),(3,),) 有什么区别？
"""
```

## 其他
```python
"""
元组和列表十分相似 但由于元组的子元素无法修改的特点，
所有在元组都没有修改、删除、添加的操作，只有读操作。
所以 这部分参考列表就可以。
"""
```

# 字典

## 定义
字典也可以当做是个容器，在内部可以存放数据。相比较于元组和列表，字典的元素必须是`键值对`
<https://blog.csdn.net/qq_36581961/article/details/110621888>

```python
info = {"age":12, "status":True, "name":"11","hobby":['篮球','足球']}# 例如： "age":12 称为一个键值对。
d = {}
```
> 注意：字典的键有特殊要求，即：必须可哈希 。 目前为止学到的可哈希的类型：int/bool/str/tuple；不可哈希的类型：list/dict

## 常用方法
```python
info = {"age":12, "status":True, "name":"11","hobby":['篮球','足球']}

# 所有键
info.keys()

# 所有值
info.values()

# 键值
info.items()

# 更新字典键值对
info.update({'age':13}) ## info中没有的键直接添加；有的键则更新值

# 移除字典的键值对
info.pop('age')

# 根据键获取值
info.get('name',None)
```

# 集合
## 定义
集合与元组和列表相似都用于做容器，在内部可以放一些子元素，但集合有三特殊特点： 子元素不重复 、 子元素必须可哈希、无序.
```python
v1 = {1,2,99,18}
v2 = {"aa","cc","bb","Ee"}
v3 = {1,True,"world",(11,22,33)}
```

## 常用方法
```python
data = {1,2,'a'}

# 添加元素
data.add('b')

# 删除元素
data.discard('c') #不存在不会报错

# 交集
s1 = {"aa", "bb", "cc"} 
s2 = {"aaa", "bb", "ccc"} 
s3 = s1 & s2   # 方式一：取两个集合的交集 
s4 = s1.intersection(s2)) # 方式二：取两个集合的交集
print(s3,s4)

# 并集
s1 = {"aa", "bb", "cc"} 
s2 = {"aaa", "bb", "ccc"}} 
s3 = s1 | s2               # 方式一：取两个集合的并集 
s4 = s1.union(s2))         # 方式二：取两个集合的并集 
print(s3,s4)


# 差集
s1 = {"刘能", "赵四", "⽪⻓⼭"} 
s2 = {"刘科⻓", "冯乡⻓", "⽪⻓⼭"} 
s3 = s1 - s2                       # 方式一：差集，s1中有且s2中没有的值 
s4 = s1.difference(s2)         # 方式二：差集，s1中有且s2中没有的值 
print(s3,s4) 
s5 = s2 - s1                     # 方式一：差集，s2中有且s1中没有的值 
s6 = s2.difference(s1)           # 方式一：差集，s2中有且s1中没有的值 
print(s5,s6)
```