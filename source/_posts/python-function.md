---
title: Python 基础：函数
comments: true
date: 2020-05-18 12:44:56
categories:
- Python
tags:
- 函数
- 参数
- 作用域
---

在不知道什么是函数前，我们已经使用过函数了。

```python
print("Hello World!")
```

这就是一个使用函数的例子。专业点说，叫做调用函数。

我们来实现一个两个数字求和的需求。

```python
a = 1
b = 2
sum = a + b
print(sum) # 3
```

再实现一个较大数字的求和。

```python
a = 99
b = 100
sum = a + b
print(sum) # 199
```

可以发现，两次求和除了数字的不同，其它的代码是一样，或者说代码重复了。

这时就有了一个问题：我能够只写一遍代码，然后多次使用吗？

答案是可以的。函数就是解决这个问题的。

<!-- more -->

**函数是对可复用代码的封装。**把重复率较高的代码块封装在一个容器中，实现一次编写，多次使用，提高代码的可复用率。函数就是基于这样的想法诞生的，它就是那些重复代码的容器。

改造后的代码如下：

```python
def sum(a, b) :
    print(a + b)
```

只需要2行代码，我们便实现了求和。

做到“一次编写”了，那“多次使用”呢？

```python
sum(1, 2) # 3
sum(99, 100) # 199
```

我们已经使用2次了，更多次也是可以的。这样是不是很方便？!

## 定义函数

我们改写上面的函数。

```python
def sum(a, b) :
    return a + b
```

函数由关键字 `def` 定义，其后紧跟函数名 `sum`，注意函数名 `sum` 和 `def` 之间有空格。函数名后的括号用来放参数，`a` 和 `b` 就是参数。`return` 关键字用来返回一个值，也是函数结束的标识。

**函数是必须有返回值的。**

你可能会有疑问，刚才的函数不是没有返回值吗？

```python
# 返回值在哪里？
def sum(a, b) :
    print(a + b)
```

在函数中，如果没有显式地出现 `return` 语句时，函数默认返回值是 None。

上面的代码相当于：

```python
def sum(a, b) :
    print(a + b)
    return None
```

我们也可以检测一下函数 `sum` 返回值是什么。

```python
def sum(a, b) :
    print(a + b);
# 想一下下面的语句为什么打印两行信息？
print(sum(1, 2));
# 3
# None
```

**return 后可以跟多个值。**

上面的例子中 return 后面只有单个值，它后面也可以跟多个值。

```python
def return_many() : 
    return 1, 2, 3
```

我们来看一下返回的情况。

```python
print(return_many()) # (1, 2, 3)
print(type(return_many())) # <class 'tuple'>
```

我们可以发现，在 return 后面出现多个值时，函数返回值是一个包含所有值的元组。也就是说，无论 return 后面出现 1 个还是多个值，函数只返回一个值。

## 函数参数

Python的参数类型有位置参数、默认参数、可变参数、关键字参数和命名关键字参数，我们可以灵活地使用不同类型的参数来解决不同的需求。这些参数可以组合使用，但是填入括号的顺序是有限制的。参数定义的顺序必须是：位置参数、默认参数、可变参数、命名关键字参数和关键字参数。当然，函数是可以没有参数的，这里讨论的是有参数的情况。

### 位置参数

我们有一个求某数字平方的需求，这个函数该怎么写？

```python
def square(number) :
    return number * number
```

验证函数是否满足需求。

```python
print(square(2)) # 4，2 的平方是 4，我们的函数没有问题。
```

这个 `number` 就是位置参数，也叫必选参数。个人更倾向于前一个说法。在定义了位置参数的情况下，调用函数时就必须传参，否则就会报错。这就是其必选的意义。注意，必选参数的意义不是说函数里必须有这种参数。

```python
square() # TypeError: square() missing 1 required positional argument: 'number'
```

### 默认参数

我们有一个求任意数字的任意次方的需求，该怎么实现？

```python
def power(number, times) :
    return number ** times
```

这个时候的 2 次方就变成这样了：

```python
power(2, 2) # 4
```

其它次方也可以获得：

```python
# 3次方
power(2, 3) # 8
# 4次方
power(2, 4) # 16
```

一般情况下，2次方使用的频率是最高的。在使用2次方时，我可以不传2吗？这样可以提高效率。

这时候就需要函数的默认参数了。使用默认参数改写的代码如下：

```python
def power(number, times = 2) :
    return number ** times
```

验证一下我们的函数，注意第一行的代码。

```python
power(4) # 16 等价于 power(4, 2)
power(4, 2) # 16
power(4, 3) # 64
```

`times = 2` 就是函数中的默认参数。在定义时带有值的参数，就是默认参数，即有默认值的参数。在传参时，如果默认参数没有接收到值，默认参数会将其在定义的值传入函数体。

`power` 函数在定义时期望接收两个参数。在 `power(4)` 中，`power` 函数发现只有一个参数传过来，按照参数从左到右的顺序赋值，它把 4 分配给必选参数 `number` 后，就没有参数可传了。默认参数 `times` 在等待赋值时，发现没有值传过来，就把自己的默认值 2 传到 `return numbers ** times` 这个函数体中。

### 可变参数

顾名思义，可变参数就是个数不确定的参数。我们来看一个求和函数的变体。

```python
def sum (*args) :
    result = 0
    for i in args :
        result += int(i)
    return result
```

验证函数是否能够求和。

```python
sum(1, 2, 3) # 6
```

上例中的 `*args` 就是可变参数，`*` 是可变参数的标志，`args` 是可变参数名，它是一个元组，用来存放传进来的任意个数的参数。`args` 是一个约定俗成的写法，我们也可以写成其它的变量名。回顾一下我们是怎么操作元组的，就知道怎么在函数体内操作可变参数了。

**可变参数的个数可以为0**，即函数定义中有可变参数，但是可以不向可变参数传参。

```python
sum() # 0
```

既然 `args` 是一个元组，那在传参时，可以直接传一个元组吗？答案是肯定的。

```python
# 在传参时，元组变量名需要加 * 号
tuple1 = (1, 2, 3)
sum(*tuple1) # 6
```

此外，不仅可以传递一个元组，还可以传递列表和字符串等序列集合。

```python
# 传递列表
list1 = [1, 2, 3]
sum(*list1) # 6
# 传递字符串
str1 = "123"
sum(*str1) # 6
```

### 关键字参数

可变参数允许你传入 0 个或任意个参数，这些可变参数在函数调用时自动组装为一个元组。而关键字参数允许你传入 0 个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个字典。

我们定义一个管理学生信息的函数，并传参调用它。

```python
def students(name, age, **kw) :
    print("学生姓名是%s，年龄是%d岁" % (name, age), end = "，")
    print("其它信息是：", kw)

# 学生姓名是Lee，年龄是18岁，其它信息是： {'city': '深圳'}
students("Lee", 18, city="深圳")

# 关键字参数为空
students("Lee", 18) # 学生姓名是Lee，年龄是18岁，其它信息是： {}
```

上例中的 `**kw` 就是关键字参数，`**` 是关键字参数的标志，`kw` 是关键字参数名，它是一个字典，用来存放传进来的键值对参数。`kw` 是一个约定俗成的写法，我们也可以写成其它的变量名。回顾一下我们是怎么操作字典的，就知道怎么在函数体内操作关键字参数了。

既然 `kw` 是一个字典，那在传参时，可以直接传一个字典吗？答案是肯定的。

```python
# 传参时，要在字典变量名前加 **
dict1 = {"city": "深圳"}
students("Lee", 18, **dict1) # 学生姓名是Lee，年龄是18岁，其它信息是： {'city': '深圳'}
```

### 命名关键字参数

在关键字参数中，可以传递任意的键值对。如果要对键值对做约束，只允许传递特定的键值对，就得用到命名关键字参数。命名关键字参数需要一个特殊分隔符 `*`，`*` 后面的参数被视为命名关键字参数。

我们来改写上例中的函数。

```python
def students(name, age, *, city, school) :
    print("学生姓名是%s，年龄是%d岁" % (name, age), end = "，")
    print("所在城市是%s，学校是%s。" % (city, school))

# 调用并传参
students("Lee", 18, city="深圳", school="第一中学")
```

分隔符 `*` 后的 `city` 和 `school` 就是命名关键字参数。在向命名关键字传参时，必须带上参数名，也就是要以键值对的方式传参， 如 `city="深圳"` 和 `school="第一中学"`。

如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符`*`了。

```python
def students(name, age, *args, city, school) :
    print("学生姓名是%s，年龄是%d岁" % (name, age), end = "，")
    print("所在城市是%s，学校是%s。" % (city, school))

# 调用并传参
students("Lee", 18, city="深圳", school="第一中学")
```

在上例中，`city` 和 `school` 还是命名关键字参数。

**命名关键字参数可以有默认值。**

```python
def students(name, age, *args, city="深圳", school) :
    print("学生姓名是%s，年龄是%d岁" % (name, age), end = "，")
    print("所在城市是%s，学校是%s。" % (city, school))

# 调用并传参
students("Lee", 18, school="第一中学")
```

这有点类似于默认参数的用法，但是 `city` 是一个命名关键字参数。它在 `*` 或者 `*args` 后面。

我们得注意几个 `*` 号的区别：

+ `*` ：表示命名关键字参数
+ `*args`：表示可变参数
+ `**kw`：表示关键字参数

## 匿名函数

顾名思义，匿名函数就是指没有函数名的函数。它由关键字 `lambda` 定义。

使用 `lambda` 来改写求和函数。

```python
def my_sum(a, b) :
    return a + b
# 改写
lambda a, b : a + b
```

冒号左边的 `a`，`b` 就是参数，右边的 `a + b` 就是函数返回值。

匿名函数也是一个函数对象，也可以把匿名函数赋值给一个变量，再利用变量来调用该函数。

```python
my_sum = lambda a, b : a + b
print(my_sum) # <function <lambda> at 0x00000000024191F8>
my_sum(1, 2) # 3
```

匿名函数也可以作为函数返回值。

```python
def my_sum(a, b) :
    return lambda a, b : a + b

isLambda = my_sum(1, 2)
print(isLambda) # <function sum.<locals>.<lambda> at 0x0000000001E59438>
print(isLambda(1, 2)) # 3
```

## 函数注解



## 高阶函数



## 闭包



## 装饰器

一块简陋的麻布披在身上，就能起到遮羞和保暖的效果。后面衣服出现花纹图案等装饰，衣服不仅起到了保暖和遮羞的作用，还具有了观赏的美学效果。

装饰器类似于衣服上的花纹和图案。装饰器是一个函数，这个函数接收另一个函数（好比衣服）作为参数，这个作为参数的函数即是被装饰的对象。

对于前面的求和函数来说，我们求和的目的已经达到了。假设有这样的一种情况，不再改变求和函数，执行求和函数后直接在控制台打印出两数之和。

我们想要这种结果：

``` python
my_sum(1, 2)  # 直接打印出 3
```

而不是通过 `print(sum(1, 2))` 来打印出 3 。

我们通过下面的例子来感受下装饰器是什么。

我们先定义一个函数。

``` python
def print_sum(func):
    def print_my_sum(*args):
        print(func(*args))
    return print_my_sum
```

定义求和函数时在函数上加上一行代码：

```python
@print_sum
def my_sum(a, b):
    return a + b

my_sum(1, 2)  # 直接打印出 3
```

可以看到，我们的目的实现了。这就是使用装饰器的一个例子。`my_sum` 函数体内没有任何的改动，我们只是加了一句 `@print_sum` 就修改了原来 `my_sum` 的某些行为。在这里，`@print_sum` 就是使用装饰器的语法。我们知道 `print_sum` 是一个我们自定义的函数，当这个函数以 **@函数名**的形式出现在另一个函数的定义中时，这个函数就是一个装饰器。

对于装饰器，要有一些最基本的认识：

+ 装饰器是一个函数
+ 装饰器是一种对已存在能力的强化剂或者改造器

通过阅读上面的代码，我们可以发现， `my_sum` 函数的函数体没有任何变化，我们在执行 `my_sum` 时，执行的函数其实是在 `print_sum` 函数中定义的 `print_my_sum` 函数。也就是说，通过装饰器函数`print_sum` 的装饰, `my_sum` 函数已经被强化或者改造了。

``` python 
@print_sum
def my_sum(a, b):
    return a + b

# 等价于
my_sum = print_sum(my_sum)
my_sum(a, b)
```

可以看到，被装饰的函数 `my_sum` 是作为参数传递给 `print_sum` 这个装饰器函数的。