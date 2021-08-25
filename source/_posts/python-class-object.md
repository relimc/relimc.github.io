---
title: Python 基础：类与对象
comments: true
date: 2020-05-19 15:15:00
categories: 
- Python
tags: 
- 类
- 对象
---

我们应该都听过类似的话：“他们是一个模子刻出来的”。这句话可以说是道出了面向对象的真谛。在面向对象编程中，类（class）和对象（object）是两个非常重要的概念，其它概念基本上都是由这两个概念延伸出来的。

它们的关系是怎么样的呢？类就好比是那个“模子”，对象就是“他们”，专业术语就是：对象是类的实例。

我们可以通过印钞机和钞票的关系来理解类和对象的关系。印钞机内有个100块人民币的模板，印钞机根据这个模板就可以印出大量的100块纸币，只是每张纸币的编号不同。那个模板就是类，每一张100块纸币就是一个实例。类只有一个，根据类创造出来的对象可以有多个，对象与对象之间很相似。

{% asset_img python-class-object.gif "类和对象的关系例子：印钞机与钞票的关系" %}

<!-- more -->

## 再议内置数据类型

Python 内置的数据类型有6种：

- Number（数字）
- String（字符串）
- List（列表）
- Tuple（元组）
- Set（集合）
- Dictionary（字典）

Python3 的六个标准数据类型中：

- **不可变数据（3 个）：**Number（数字）、String（字符串）、Tuple（元组）；
- **可变数据（3 个）：**List（列表）、Dictionary（字典）、Set（集合）。

type 函数用于查看某个对象是属于哪个类型的。

```python
print(type(int()))
print(type(""))
print(type([]))
print(type(()))
print(type({}))
print(type(set()))
# <class 'int'>
# <class 'str'>
# <class 'list'>
# <class 'tuple'>
# <class 'dict'>
# <class 'set'>
```

读到这里，发现什么了吗？原来我们之前已经接触了类和对象了。

+ 空数字是 int 类的实例
+ 空字符串是 str 类的实例
+ 空列表是 list 类的实例
+ 空元组是 tuple 类的实例
+ 空字典是 dict 类的实例
+ 空集合是 set 类的实例

我们目前为止只接触了内置的数据类型，那么我们能自定义自己需要的数据类型吗？答案当然是可以啊。类和对象的相关知识就是来解决这个问题的。

## 定义类和对象

类由 `class` 关键字定义。我们来定义一个 `student` 类。

```python
class student :
    pass
```

创建对象的过程叫做类的实例化。我们来创建一个 `student` 类对象 `lee`。

```python
lee = student()
type(lee) # <class '__main__.student'>
```

实例化的过程是否似曾相识？这里怎么那么像函数的调用，`student` 是一个函数吗？

### `__new__()`

在函数体中没有 return  字句时，Python 会默认添加一句 `return None`。

在类实例化时，如果没有显式地定义 `__new__()` 函数，Python 会默认自动调用 `__new__()` 函数。

如同函数一定有返回值一样，类也一定有 `__new()__` 函数。这是一个特殊的方法，它叫做构造函数（方法），**它是实例化过程中第一个被调用的方法。** 在类中，函数被称为方法。

我们在实例化一个类生成一个对象时，默认就是调用 `__new__()`构造方法。

现在我们来解释 `student()` 。`student` 是一个类名，并不是一个函数名。`student()`表示调用 `student` 类的 `__new__()` 构造方法来创建一个对象。

我们来检测一下实例化类时是否调用了 `__new__()` 构造函数。

```python
class student :
    def __new__(cls) : # 显式地定义 __new__
        print("我在类实例化一个对象时自动调用")
        return super().__new__(cls)
# 实例化一个对象
lee = student() # 我在类实例化一个对象时自动调用
type(lee) # <class '__main__.student'>
```

看到没有，我们并没有做任何直接调用 `__new__()` 的步骤，但是 `__new__()` 被调用了。它什么时候被调用呢？就是代码执行到 `student()` 时。记住：**实例化一个类时，`__new__`方法总是会被执行。** 其实正确的说法是，`__new__`构造方法能创建并返回一个类的实例，当一个实例被创建出来，说明 `__new__()` 方法已经执行过了。

我们得注意，上例中 `__new__(cls)` 中的  `cls`（class 的简写）参数是必须有的。`cls` 表示正在被实例化的类。

这里已经涉及了继承相关的知识了。我们在 `student` 类中显式地定义 `__new__()` 方法，其实是 `student` 类覆盖了父类的 `__new__` 方法。这叫做方法的重写（override）。在重写 `__new__()` 方法后必须要有返回值，这个返回值是实例化出来的对象，返回的通用写法就如 ` return super().__new__(cls)` 所示，这行代码的意思是：把当前类 `cls` 传给父类的 `__new__()`方法，由父类来创建 `student` 类的对象，最后把该对象返回。

我们可以试一下 `__new__()` 方法没有返回值的情况。

```python
class student :
    def __new__(cls) : # 显式地定义 __new__
        print("我在类实例化一个对象时自动调用")
        # return super().__new__(cls)
# 实例化一个对象
lee = student() # 我在类实例化一个对象时自动调用
type(lee) # <class 'NoneType'>
```

在没有返回值的情况下，程序没有报错，只是生成的实例属于 `NoneType` 类，而不是我们定义的 `student` 类。

### 对象的生命周期

对象的生命周期主要包括创建、初始化和销毁。

## 属性和方法

前面提到了方法，`__new__()`就是一个类方法。此外，类还有属性。属性和方法都分为实例层面的和类层面的，即有实例的属性和实例的方法、有类的属性和类的方法。所有者与所有者的属性和方法之间用 `.`连接，如 `类.属性`。相信我们已经有了变量和函数的概念，在类中，我们把变量称为属性，把函数称为方法。也就是说，我们其实并没有学习新的语法，只是原来的概念换了而已。

### 类的属性和方法

#### 类属性

我们来改写 `student` 类，在类主体内定义并赋值一个变量，这个变量就是类的属性。

```python
class student :
    school_name = "第一中学"
```

我们无需实例化一个对象即可访问类属性，即类属性与对象无关。

```python
print(student.school_name) # 第一中学
```

当然我们也可以通过实例对象来访问类属性，只是这样就不好区分当前属性是类属性还是实例属性。

```python
# 不推荐
lee = student()
print(lee.school_name) # # 第一中学
```

#### 类方法

定义类的方法不能像定义函数那样，它还需要一个称为装饰器的东西。先要有一个装饰器，然后再像定义函数那样去定义类的方法。我们来改写 `student` 类。

```python
class student :
    @classmethod
    def print_school(cls) :
        print("我是一个类方法", end = "，")
        print("我的类名是：{0}".format(cls.__name__))
```

上例中，`@classmethod` 就是一个装饰器，由这个 `@classmethod` 装饰器定义出来的函数就是类方法。关于装饰器的内容，可移步[Python 函数装饰器](https://www.runoob.com/w3cnote/python-func-decorators.html)了解。

前面我们定义（重写）类方法 `__new__()`是这样写的：

```python
def __new__(cls) : # 显式地定义 __new__
    print("我在类实例化一个对象时自动调用")
    return super().__new__(cls)
```

跟上面的 `print_school(cls)` 方法有什么异同吗？

不同点是，在重写 `__new__(cls)` 方法时并没有使用装饰器。这是因为 `student` 的父类 `object` （object 是所有类的父类）在定义 `__new__()` 方法时已经使用装饰器了，子类 `student`在重写该方法时就不需要再次使用装饰器了。

相同点是，它们都有一个 `cls` 参数来表示当前这个类。这个 `cls` 也是类方法在定义时必不可少的，它必须作为第一个参数传进类方法中。因此，类方法的定义可以进一步限定为：由这个 `@classmethod` 装饰器定义出来的，且第一个参数是类本身的函数。

此外，我们要注意的是 `cls` 参数其实是装饰器的参数。这是什么意思？如果你了解过装饰器相关的知识，就应该知道装饰器其实也是一个函数。我们定义的 `print_student()` 方法将作为参数传递给装饰器。我们来看一下 `@classmethod` 的源码：

```python
class classmethod(object):    
    def __init__(self, function): # real signature unknown; restored from __doc__
        pass
```

`__init__(self, function)` 方法接收两个参数：`self` 和 `function`。类方法 `print_student(cls)` 中的 `cls`  是传给 `self` 参数的，`pirnt_student` 函数是传给 `function` 的。也就是说，类方法的第一个参数是属于装饰器的，第二个参数开始，才是属于类方法的参数。

类方法定义出来了，接下来，我们来调用类方法。类方法与对象无关，我们无需创建对象。

```python
# 在定义 print_school 时没有第二个参数，所以不用传参
student.print_school() # 我是一个类方法，我的类名是：student
```

当然，我们可以通过类的对象来访问类方法，只是这样就不好区分当前方法是类方法还是实例方法。

```python
lee = student()
lee.print_school() # 我是一个类方法，我的类名是：student
```

### 实例属性和实例方法

#### 实例属性

在说实例属性之前，我们来回顾一下类属性。

```python
class student :
    school_name = "第一中学"
```

在类主体内直接定义一个变量，这个变量就是类属性。那我们去哪里定义实例属性呢？我们得要使用 `__init__()` 方法。

##### `__init__()`

同 `__new__()` 方法一样 ，在类实例化时，Python 会自动调用`__init__()` 方法。它们的关系如下：

+ `__new__` 方法在 `__init__` 方法前调用
+ `__new__`  方法称为构造方法，`__init__`  方法称为初始化方法
+ `__new__`  方法返回值是类初始化生成的实例，`__init__`  方法的第一个参数是类初始化生成的实例
+ `__new__`  方法创建一个对象，对象不带属性，`__init__` 方法初始化这个对象，可以给对象添加属性

我们来验证一下上面的结论。

```python
class student :
    def __new__(cls):
        new_object = super().__new__(cls)
        print('''我是 __new__ 函数，我能创建一个对象，我首先被调用。
        我生成的对象 id 是 {0}'''.format(id(new_object)))
        return new_object
    
    def __init__(self):
        print('''我是 __init__ 函数，我在对象创建后初始化这个对象，我得在对象生成后才被调用。
        我得到的对象 id 是 {0}'''.format(id(self)))

# 类实例化一个对象
lee = student()
'''
我是 __new__ 函数，我能创建一个对象，我首先被调用。
我生成的对象 id 是 32010440
我是 __init__ 函数，我在对象创建后初始化这个对象，我得在对象生成后才被调用。
我得到的对象 id 是 32010440
'''
```

可以发现，上面的结论没有错：

+ `__new__` 方法首先被调用

+ `__new__` 方法生成的对象的确是 `__init__` 方法的第一个参数。

现在我们把关注点放在两个方法的参数上：`cls` 和 `self`。

+ `cls` 是类，是当前正在被实例化的类
+ `self` 是对象，是类实例化后生成的对象

+ `cls` 总是作为 `__new__()` 方法定义时的第一个参数
+ `self` 总是作为 `__init__()` 方法定义时的第一个参数

在前面，我们已经证明 `cls` 是当前类。现在我们再单独看一下 `self`。

我们来改写 `student` 类。

```python
class student:
    def __init__(self) : # 显式地定义 __init__
        print("我在类实例化一个对象时自动调用")
        print(self)
        print(self.__class__) # self 的类型
        print(type(self)) # self 的类型
        
# 类实例化一个对象
lee = student()
# 我在类实例化一个对象时自动调用
# <__main__.student object at 0x0000000001E83D08>
# <class '__main__.student'>
# <class '__main__.student'>
```

看见没？ `__main__.student object` 就是表示 `self` 是 `student` 类的一个 `object`，是一个类对象。

现在，我们应该能够知道：`lee` 等于 `self` 。通过代码验证一下：

```python
class student:
    def __init__(self) : # 显式地定义 __init__
        print(id(self)) # 获取 self 的内存地址
# 实例化一个对象
lee = student()
print(id(lee)) # 获取 lee 的内存地址
# 两个 id 一样
# 35339528
# 35339528
```

我们可以好好地回味一下 `lee = student()` 这行代码。

##### 定义实例属性

`lee` 是一个实例，`self` 也是一个实例，它们两个是同一个对象。我们要在类中定义实例属性，通过 `self` 就可以做到了。

```python
class student :
    def __init__(self, name, age) : # 初始化实例对象
        self.name = name # 定义一个 self.name 属性
        self.age = age # 定义一个 self.age 属性
lee = student('Lee', 18)
print(f"学生 lee 的姓名是{lee.name}，学生 lee 的年龄是{lee.age}")
# 学生 lee 的姓名是 Lee，学生 lee 的年龄是 18
```

注意：`__init__(self, name, age)` 中第一个参数默认是指实例本身（无论名字是否是 `self`）。也就是说，如果 `__init__()` 方法有 n 个参数，那我们只需要传 （n - 1） 个值即可，如 `student('Lee', 18)`。

我们知道，创建对象的过程称为类的实例化。现在我们应该能知道，实例化其实**包含 `__new__` 方法生成并返回对象和 `__init__` 方法初始化对象**的过程。

#### 实例方法

上文中的 `__init__()` 就是一个实例方法。实例方法的定义比较简单，在类主体中像定义函数那样直接定义方法即可。不过，与函数还是有区别的，实例方法的第一个参数必须存在而且是该实例本身，这个参数的约定俗成的写法就是 `self`。

```python
class student :
    def instance_method(self) :
        print("我是一个实例方法，不能被类直接调用")
        print(f"self 参数的类型是：{type(self)}")
lee = student()
lee.instance_method()
# 我是一个实例方法，不能被类直接调用
# self 参数的类型是：<class '__main__.student'>
```

### 内置属性


上文写的是自定义属性的例子，Python 还有许多内置的类属性和内置的实例属性。

#### \_\_class__

这个内置属性既是类属性，也是实例属性。这个属性表示类属性时，返回 `<class 'type'>`。这个属性表示实例属性时，返回构建这个实例对象的类。


``` python
# 类属性
print(student.__class__)  # <class 'type'>

# 等价于
print(type(student)  # <class 'type'>

# 实例属性
zhangsan = student()
print(zhangsan.__class__)  # <class '__main__.student'>
```

其实，`__class__ ` 可以理解为一个实例属性，实例调用__class__属性时会指向该实例对应的类。所有的类都是 type 类的实例，所以 `student.__class__` 指向 `type` 类，`zhangsan` 是 `student` 类的实例，`zhangsan.__class__` 指向 `student` 类。


#### \_\_base__

这个是类属性，它返回类的父类。

``` python
class A:
    pass

class B(A):
    pass

print(A.__base__)  # <class 'object'>
print(B.__base__)  # <class '__main__.A'>
print(B.__base__.__base__)  # <class 'object'>
```

#### \_\_dict__

这个属性返回一个字典。

它作为类属性时，返回类所有的静态方法、类方法、实例方法和自定义的类属性。它还返回一些内置的属性。注意：它并不返回实例属性。

它作为实例属性时，返回 `__init__` 初始化对象方法中定义的实例属性。

``` python
class A(object):
    """
    Class A.
    """

    a = 0
    b = 1

    def __init__(self):
        self.a = 2
        self.b = 3

    def test(self):
        print('a normal func.')

    @staticmethod
    def static_test(self):
        print('a static func.')

    @classmethod
    def class_test(self):
        print('a class func.')


a = A()
print(A.__dict__)  # {...'a': 0, 'b': 1...} 没有完全展示，可自己复制代码查看结果
print(a.__dict__)  # {'a': 2, 'b': 3}
```

#### \_\_name__

这是一个类属性，返回类的名称。

``` python
class A:
    pass

print(A.__name__)  # A
print(A().__name__)  # 报错，它不是一个实例属性
```


### 属性与键



## 继承



