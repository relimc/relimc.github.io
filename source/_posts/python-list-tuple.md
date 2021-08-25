---
title: Python 基础：列表与元组
comments: true
date: 2020-05-18 10:30:27
categories:
- Python
tags:
- 数据类型
- 列表
- 元组
---
列表和元组都是Python内置的数据类型，它们都是有序的集合。列表由方括号表示，方括号内可以包含多个列表元素。我们可以对里面的元素进行增删改查的操作。元组由括号表示，括号内可以包含多个元组元素。我们只可以对里面的元素进行查询操作。

<!-- more -->

## 列表

列表是一个可变长序列，意味着其元素个数是不确定的，其某个元素的索引也是不固定的。列表的元素可以相同。

### 操作元素

#### 查找元素

列表的元素都有一个属于其自己的索引，查找元素就是通过索引来获取元素值。索引分正负值，为正时，0表示第一个索引，最后一个索引为（元素个数 -1）；为负时，-1 表示最后一个元素的索引，第一个元素的索引为元素个数的负值。

先新建一个用例列表。

```python
lists = ["apple", "banana", "cat", "banana", "apple"];
```

对应的索引是：

![Yrcz79.jpg](https://s1.ax1x.com/2020/05/15/Yrcz79.jpg)

获取所有元素的个数

```python
len(lists); # 5
```

查找列表中最大的元素

```python
max(lists) # cat
```

查找列表中最小的元素

```python
min(lists) # apple
```

查找第一个元素

```python
print(lists[0]) # apple
# 或者
print(list[-5]) # apple
```

查找最后一个元素

```python
print(list[4]) # apple
#或者
print(list[-1]) # apple
```

查找某个元素在列表中出现的个数

```python
lists.count("apple") # 2
lists.count("cat") # 1
```

查找某个元素在列表中第一次出现的索引

```python
lists.index("apple") # 0
```

在查找元素时要注意索引越界的问题，索引越界会导致报错。索引越界是指出现列表中不存在的索引。

```python
# list 列表中没有索引5
list[5] # IndexError: list index out of range
```

#### 添加元素

添加一个元素

```python
# 新建 letters 列表，并向其中添加一个元素
letters = ["a", "b", "c"]
letters.append("d") 
print(letters) # ['a', 'b', 'c', 'd']
```

元素允许是不同类型的

```python
# 数值类型
letters.append(0) 
print(letters) # ['a', 'b', 'c', 'd', 0]

# 列表类型
letters.append([1, 2])
print(letters) # ['a', 'b', 'c', 'd', 0, [1, 2]]
```

添加多个元素

```python
# extend 函数接收一个序列集合

# 列表序列
letters.extend([3, 4, 5])
print(letters) # ['a', 'b', 'c', 'd', 0, [1, 2], 3, 4, 5]

# 元组序列
letters.extend((6, 7))
print(letters) # ['a', 'b', 'c', 'd', 0, [1, 2], 3, 4, 5, 6, 7]

# 字符序列
letters.extend("xyz")
print(letters) # ['a', 'b', 'c', 'd', 0, [1, 2], 3, 4, 5, 6, 7, 'x', 'y', 'z']
```

**append** 和 **extend** 都是往列表尾部添加元素，我们还可以往列表的任意处添加一个元素

```python
# 新建一个列表并在列表中插入一个元素
numbers = [1, 3, 4]
numbers.insert(1, 2) # 在索引1处添加元素2，后面的元素索引都受影响
print(numbers) # [1, 2, 3, 4]
```

在 Python 中，不能使用索引来新增列表元素，因为索引存在越界报错的问题。我们通过一个例子来说明。

```python
list1 = []
list1[0] = 1 # IndexError: list index out of range
```

空数组没有索引，当使用 `list1[0]` 时，就会出现索引越界的错误。

#### 删除元素

拿上文的 letters 列表来举例，先查看现在的元素情况

```python
print(letters) # ['a', 'b', 'c', 'd', 0, [1, 2], 3, 4, 5, 6, 7, 'x', 'y', 'z']
```

删除一个元素

```python
letters.pop() # 默认删除最后一个元素
print(letters) # ['a', 'b', 'c', 'd', 0, [1, 2], 3, 4, 5, 6, 7, 'x', 'y']

# pop 方法返回被删除的那个元素
deleted = letters.pop()
print(letters) # ['a', 'b', 'c', 'd', 0, [1, 2], 3, 4, 5, 6, 7, 'x']
print("deleted is %s." % deleted) # deleted is y.
```

删除指定的某个元素

```python
# pop 方法可以接收一个索引参数，删除该索引对应的值
letters.pop(-2) # 删除倒数第二个元素7
print(letters) # ['a', 'b', 'c', 'd', 0, [1, 2], 3, 4, 5, 6, 'x']

# remove 方法可以接收一个元素，移除列表中该元素的第一个匹配项
letters.extend("yzx") # 先追加三个字符，其中有个 x
letters.remove('x'); # 删除 x，查看被删除的是那个 x
print(letters) # ['a', 'b', 'c', 'd', 0, [1, 2], 3, 4, 5, 6, 'y', 'z', 'x']

# remove 没有返回值
x = letters.remove('x')
print(letters); # ['a', 'b', 'c', 'd', 0, [1, 2], 3, 4, 5, 6, 'y', 'z']
print("x is deleted?", x) # x is deleted? None
```

清空列表

```python
letters.clear() # clear 方法用于删除所有元素
print(letters) # []
# 或者
letters = ["a", "b", "c"]
print(letters)
del letters[:] # 使用 del 删除
print(letters)
```

#### 更新元素

我们使用第一个例子来说明。

```python
lists = ["apple", "banana", "cat", "banana", "apple"];
```

将第 4 个元素 banana 改为 dog。

```python
lists[3] = "dog"
print(lists) # ['apple', 'banana', 'cat', 'dog', 'apple']
```

将第 5 个元素 apple 改为 fish

```python
lists[-1] = "fish"
print(lists) # ['apple', 'banana', 'cat', 'dog', 'fish']
```

### 操作列表

前文提到的增删改查均是在元素层面上的操作，下面我们进入列表层面的操作。关注点不是在列表元素里，而是在列表本身或者多个列表里。

#### 列表相加

```python
list1 = [1, 2, 3]
list2 = [4, 5, 6]
lists = list1 + list2; # [1, 2, 3, 4, 5, 6]
# 这等价于
list1.extend(list2)
lists = list1 # [1, 2, 3, 4, 5, 6]
```

#### 列表相乘

```python
li = ["hi"]
lists = li * 3; # ['hi', 'hi', 'hi']
```

#### 列表翻转

```python
lists = [1, 2, 3, 4, 5]
lists.reverse()
print(lists) # [5, 4, 3, 2, 1]
```

#### 列表复制

```python
lists = [1, 2, 3, 4, 5]
another = lists.copy()
print(another) # [1, 2, 3, 4, 5]
```

#### 列表排序

```python
lists = [13, 2, 5, 1, 35]
lists.sort() # 默认是由小到大排序
print(lists) # [1, 2, 5, 13, 35]

# 降序
lists.sort(reverse = True)
print(lists) # [35, 13, 5, 2, 1]

"""
sort 参数情况如下：sort( key=None, reverse=False)
key 值可以为一个函数名,函数的返回值作为排序依据
"""
# 取序列中的第二个元素
def getSecond(lists) :
    return lists[1]

lists = [(2, 2), (3, 1), (1, 4), (2, 3)]
# getSecond 函数接收 lists 列表元素作为参数
# 返回的第二个元素作为排序依据
lists.sort(key = getSecond)
print(lists) # [(3, 1), (2, 2), (2, 3), (1, 4)]
```

#### 列表推导

## 元组

元组是一个固定序列，一旦初始化就不能改变。与列表的可变相比，元组的数据具有较好的安全性。同列表一样，其元素也是允许重复的。

### 创建元组
### 查看元素
### 元组拆包
### 具名元组
下面写第一个元组实例。

```python
tuple1 = (1, 2, 3, 1, 2, 3)
print(tuple1) # (1, 2, 3, 1, 2, 3)
```

空元祖是这样

```python
tuple1 = ()
print(tuple1) # ()
```

注意，只包含1个元素的元组得这么写：

```python
tuple1 = (1,)
print(tuple1); # (1,)

# 仔细体会同上句的区别
not_tuple = (1)
print(not_tuple) # 1
```

编译器会把 (1) 中的括号当作算数表达式，就跟算数运算 (1+2) 中的括号一样，所以 (1) 是数字 1 的意思。我们得在第一个元素后面添加`,`来告诉编译器：`(1,)`是一个元组。

同列表相比，我们无法对元组元素进行增删改的操作，只能查看元组。那些增删改列表元素的方法都不能使用。

同列表一样，元组也是使用索引来查看某个元素，那些查看列表的方法也是适用于元组的。

### 深入理解元组的不变特性

来写一个例子。

```python
list1 = [3, 4]
tuple1 = (1, 2, list1, 6) # 等价于 tuple1 = (1, 2, [3, 4], 6)
```

尝试修改第一个元素

```python
tuple1[0] = '1' # TypeError: 'tuple' object does not support item assignment
```

我们来修改 list1，向 list1 中添加一个数字 5。

```python
list1.append(5)
print(tuple1) # (1, 2, [3, 4, 5], 6)
```

发现了什么？明明我只对 list1 做了修改，tuple1 却发生了一些变化。

不是说元组不可改变吗？元组不变的究竟是什么？

**元组不变的是元素在内存中所占的空间。**

我们来检测元组中的 [3, 4] 和 [3, 4, 5] 是不是指同一个内存空间。

```python
list1 = [3, 4]
tuple1 = (1, 2, list1, 6)
id1 = id(tuple1[2]) # 获取[3, 4]内存空间id

list1.append(5) # [3, 4, 5]
print(tuple1)
id2 = id(tuple1[2]) # 获取[3, 4, 5]内存空间id

if id1 == id2 :
    print("我的内存空间没变哦！")
```

对于元组来说，其第三个元素是没变的，变的只是第三个元素本身。

### 具名元组

这部分内容可在这里阅读：[Python namedtuple](https://www.runoob.com/note/25726)