---
title: 详谈python中的几个高阶函数
date: 2021-03-03 08:53:26
tags:
    - python进阶
    - 高阶函数
categories:
    - python
cover: https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=2614262165,254938820&fm=26&gp=0.jpg
toc: true
---

**详谈python的sort、sorted、map、filter、reduce 函数**

# sort()
`sort()`函数用于对原列表进行排序，如果指定参数，则使用比较函数指定的比较函数。`list.sort(key=None, reverse=False)`
- key:主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。
- reverse:排序规则，reverse = True 降序， reverse = False 升序（默认）
```python
l = [(2, 2), (3, 4), (4, 1), (1, 3)]
l.sort(key=lambda x: x[1], reverse=True) # 按照元祖的第二个元素排序（降序）
print(l)
# [(3, 4), (1, 3), (2, 2), (4, 1)]
```

# sorted()

- 对列表排序,返回的对象不会改变原列表
```python
list = [1,5,7,2,4]
sorted(list)
# [1, 2, 4, 5, 7]
#可以设定时候排序方式，默认从小到大，设定reverse = False 可以从大到小
sorted(list,reverse=False)
# [1, 2, 4, 5, 7]
sorted(list,reverse=True)
# [7, 5, 4, 2, 1]
```

- 根据自定义规则来排序，使用参数：key
```python
#使用key,默认搭配lambda函数使用
sorted(chars,key=lambda x:len(x))
#['a', 'is', 'boy', 'bruce', 'handsome']
sorted(chars,key=lambda x:len(x),reverse= True)
#['handsome', 'bruce', 'boy', 'is', 'a']
```

- 根据自定义规则来排序，对元组构成的列表进行排序
```python
tuple_list = [('A', 1,5), ('B', 3,2), ('C', 2,6)]
#key=lambda x: x[1]中可以任意选定x中可选的位置进行排序
sorted(tuple_list, key=lambda x: x[1]) 
#[('A', 1, 5), ('C', 2, 6), ('B', 3, 2)]
sorted(tuple_list, key=lambda x: x[0])
#[('A', 1, 5), ('B', 3, 2), ('C', 2, 6)]
sorted(tuple_list, key=lambda x: x[2])
#[('B', 3, 2), ('A', 1, 5), ('C', 2, 6)]
```

# map()
python的`map`函数使得函数能直接以list的每个元素作为参数传递到`funcname`中, 并返回响应的新的`list`
```python
def sq(x):
  return x*x #求x的平方
map(sq, [1,3, 5,7,9]) #[1, 9, 25, 49, 81]

map(lambda x:x**2,[1,2,3,4])
```

# filter()
`filter(function or None, iterable)`执行过程依次将list中的元素传递到funcname函数中, 根据funcname返回的True或False 保留或丢弃元素

```python
def is_int(x):
  if isinstance(x, (int)):
    return True
  else:
    return False
 
 filter(is_int, ["Yi",2, "3", 4]) #[2, 4]
```

# reduce()
与map相比 , reduce类似于一个聚合类的应用方法, 把list中的参数, 依次传递给funcname, 每次funcname的参数都是上个funcname 执行结果和下一个list中的元素, 所以, funcname 的 参数必须是两个. 从执行过程看, 有点像递归

```python
#例如: 求range(1, 101)(不包括101)的和,
def c_sum(x, y):
  return x + y;
reduce(c_sum, range(1,101)) #5050
```