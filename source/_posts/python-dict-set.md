---
title: Python 基础：字典与集合
comments: true
date: 2020-05-18 17:02:07
categories:
- Python
tags:
- 数据类型
- 字典
- 集合
---

字典 dict 与集合 set 是 Python 内置的两种数据类型。两种数据类型都用花括号表示，只是定义的方式有所区别。字典内的数据是以键值对的形式的存在，集合内的数据只有键而没有值。我们知道，列表和元组是有序的序列，字典和集合则不同，它们是无序的。字典内部存放的顺序和键放入的顺序是没有关系的，集合内的键顺序也和键被添加时的顺序没有关系。同列表和元组的元素可重复相比，字典和集合的元素是不能重复的，其实是键是不能重复的。

<!-- more -->

## 字典

字典是由任意个包含在花括号的键值对组成的。一个简单的字典如下：

```python
student = {"name": "Lee", "age": 18}
```

键值对之间使用 `,` 分割，冒号左边的是键，右边的是值。在这个例子中，键有 `name` 和 `age`，值有 `Lee` 和 `18`。

### 查找键值对

字典内单个元素包含键和值，在查找字典内的元素时，键和值都是我们要查找的对象。

#### 查找值

列表和元组是通过索引来查找对应的值，字典则是通过键来查找对应的值。

```python
# 类似列表索引的查法
student["name"] # Lee

# 或者通过 get() 方法
student.get("name") # Lee
```

#### 查找键

值可以通过键来获取，那如何获取字典的键呢？字典提供了一个 keys() 方法来获得所有的键。

```python
student.keys() # dict_keys(['name', 'age'])
```

我们可以通过 in 来判断字典是否有某个键。

```python
# students 字典是否有 name 键
"name" in student # True
```

我们也可以通过前文提到的 get() 方法来判断某个键是否存在。

```python
# 当查找的键不存在时，返回 None
student.get("na") # None
```

#### 查找所有的键值对

字典是无序的键值对集合。既然是无序的，便无法通过遍历直接获取所有的键值对。那我们该如何做呢？

前文提到有个 keys() 方法可以获得所有的键。我们也知道，只要有键，就能直到键对应的值。那么，只要 keys 获得的键的集合是可遍历的，那我们就可以获得所有的键值对了。

我们来遍历一下键。

```python
for i in student.keys() :
    print(i)
# name
# age
```

结果表明，keys() 返回的对象是可遍历的。那我们就可以改写上面的代码，实现对键值对的遍历。

```python
for i in student.keys() :
    print(i, "=", student[i])
# name = Lee
# age = 18
```

其实还有个更简单的办法来遍历字典。字典提供了一个 items() 方法用来遍历键值对。

```python
print(student.items()) # dict_items([('name', 'Lee'), ('age', 18)])

for key, value in student.items() :
    print(key, value)
# name Lee
# age 18
```

### 修改键值对

键在被添加后就无法被修改了，我们只能修改键对应的值。

```python
# 将学生的名字改为 Ming
student['name'] = "Ming"
print(student) # {'name': 'Ming', 'age': 18}
```

### 添加键值对

由于键和值之间是一一对应的关系，要添加一个键值对，首先要添加一个字典中不存在的键，然后给该键赋相应的值。如果键已存在，那不是添加操作，而是修改操作。

```python
student["address"] = "China"
print(student) # {'name': 'Lee', 'age': 18, 'address': 'China'}
```

### 删除键值对

由于键和值之间是一一对应的关系，要删除键值对，只需要删除键即可。字典提供了 pop() 方法用于删除键。

```python
# pop 的参数为键名
student.pop("address")
print(student) # {'name': 'Lee', 'age': 18}
```

### 设置默认值

当访问字典不存在的 key 时，会引发 KeyError 的错误。

```python
d = {"hello": 'world'}
d['abc']  # keyError: 'abc'
```

为了避免出现这个错误，可以设置一个默认值，当访问对象不存在的 key 时，返回这个默认值。

### dict.setdefault(key, default_key)

dict.setdefault 是一个取值操作，当 key 不存在时，它不会抛出 KeyError 错误，而是返回一个默认值。
dict.setdefault 也是修改字典的操作，当 key 不存在时，它还会新增该 key 到字典中。

``` python
d = {"hello": 'world'}
d.setdefault('a', 'default')  # default
print(d)  # {'hello': 'world', 'a': 'default'}
```

我们想要在字典 `d` 中获取键 `a` 的值，但是字典 d 其实是没有该键的，因此返回其默认值 `default`。当执行此方法后，原本的字典 `d` 发生了改变，它新增了一个键为 `a`, 其键的值为 `default`。

### dict.get(key, default_key)

dict.get 是一个取值操作，当 key 不存在时，它不会抛出 KeyError 错误，而是返回一个默认值。
与 dict.setdefault 不同的是，dict.get 不会修改字典。

``` python
d = {"hello": 'world'}
print(d.get('a', 'default'))  # default
print(d)  # {'hello': 'world'}
```

### collections.defaultdict(default_factory=None, **kwargs)

collections.defaultdist 接收一个无参函数，如 list, set, str 等工厂函数，或者是没有参数的匿名函数。defaultdict 的表面意思是默认字典，可以理解为它是一个带有默认值的字典。它与 dict() 方法类似，就是用于创建一个字典。

```python 
from collections import defaultdict
dd = defaultdict(lambda: 0)
print(dd)  # defaultdict(<function <lambda> at 0x000001A944198048>, {})
print(dd['abc'])  # 0
print(dd['def'])  # 0
print(dd)  # defaultdict(<function <lambda> at 0x000001A944198048>, {'abc': 0, 'def': 0})
```

默认字典 `dd` 没有键 `abc` 和 `def`，当访问 `dd['abc']` 和 `dd['def']` 时并没有报错，而是返回匿名函数的返回值 `0`。这个 `0` 就是默认字典 `dd` 所有键的默认值。这里的键是指不存在的键。

与 dict.setdefault 类似，collections.defaultdict 也会修改已有的字典。
默认字典 `dd` 在访问不存在的键后，如 `dd['abc']` ， `abc` 就变成了 `dd` 的新键了。

当 collection.defaultdict 接收 list 等工厂函数时，其所有键的默认值为对应类型的空值。

``` python 
from collections import defaultdict
dd = defaultdict(list)  
print(dd)  # defaultdict(<class 'list'>, {})
print(dd['abc'])  # []
print(dd['def'])  # []
print(dd)  # defaultdict(<class 'list'>, {'abc': [], 'def': []})
```

目前讨论的都是默认值的情况，我们在创建默认字典时，如何定义键值对呢？

``` pyton

from collections import defaultdict

dd = defaultdict(list, age=[8, 10, 12])
print(dd)  # defaultdict(<class 'list'>, {'age': [8, 10, 12]})
print(dd['age'])  # [8, 10, 12]
print(dd['abc'])  # []
print(dd['def'])  # []
print(dd)  defaultdict(<class 'list'>, {'age': [8, 10, 12], 'abc': [], 'def': []})
```

一般情况下，建议是：当工厂函数为 list 是，其键的值最好都是 list, 如[8, 10, 12]。当然 Python 是很灵活的语言，你要用其它类型的值也是可以的。

## 集合

集合是由任意个包含在花括号的键组成的。它由 set() 方法定义。

```python
set1 = set() # 空集合
```

非空集合的创建需要一个序列。这个序列作为参数传递给 set() 方法。

```python
# 字符序列
string = "apple"
str_set = set(string)
print(str_set) # {'p', 'e', 'l', 'a'}

# 列表序列
list = [1, 2, 3, 1]
list_set = set(list)
print(list_set) # {1, 2, 3}

# 元组序列
tuple = (1, 2, 3, 3)
tuple_set = set(tuple)
print(tuple_set) # {1, 2, 3}
```

观察上面几个集合，可以发现：集合中，只能保留一个序列中重复出现的元素。

集合中的键可以直接用循环遍历出来：

```python
for i in str_set :
    print(i)
```

集合提供 remove() 方法用来删除一个键。

```python
str_set.remove("a")
print(str_set) # {'p', 'e', 'l'}
```

集合提供 add() 方法用来添加一个键。

```python
# 连续添加两个 a 看是否能添加重复的键
str_set.add("a")
str_set.add("a")
print(str_set) # {'e', 'a', 'p', 'l'}
```

集合添加的键必须是不可变对象。

```python
# 元组是不可变对象
str_set.add(tuple)
print(str_set) # {'l', (1, 2, 3, 3), 'p', 'a', 'e'}

# 列表是可变对象，添加时报错
str_set.add(list) # TypeError: unhashable type: 'list'
```