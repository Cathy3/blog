---
title: python 魔法方法
date: 2019-03-14
categories: 
	    -   python
tags:  
        -   python
---

Python 中有的方法的名称前后都有两个下划线，这些方法被称为魔法方法。如果对象实现了这些方法中的某一个，那么这个方法就会在特殊的情况下被 Python 所调用，而这一切都是自动发生的。

这里总结一些重要的魔法方法：
-   构造方法 `__init__` ,  `__new__`

<!-- more -->

# 构造方法
## `__init__`

在定义类的时候，会去定义构造方法。当一个对象被创造后，会立即调用构造方法，定义这个对象的初始值。

```python
class FooBar:
    def __init__(self,s=8):
        self.var = 40
        self.some = s

f = FooBar(9)
print(f.var) # 40
print(f.some) # 9
```
可以给构造函数传几个参数。

## `__new__`
这个方法我们一般很少定义，不过我们在一些开源框架中偶尔会遇到定义这个方法的类。实际上，这才是“真正的构造方法”，它会在对象实例化时第一个被调用，然后再调用`__init__`，它们的区别主要如下：

-   `__new__`的第一个参数是`cls`xz，而`__init__`的第一个参数是`self`
-   `__new__`返回值是一个实例，而`__init__`没有任何返回值，只做初始化操作
-   `__new__`由于是返回一个实例对象，所以它可以给所有实例进行统一的初始化操作
-   由于 `__new__` 优先于 `__init__` 调用，且返回一个实例。

看下面例子：
```python
class Person(object):
    def __new__(cls, *args, **kwargs):
        print("in __new__")
        instance = object.__new__(cls)
        return instance

    def __init__(self, name, age):
        print("in __init__")
        self._name = name
        self._age = age

p = Person("Wang", 33)
#输出：
#in __new__
#in __init__
```




可以每次返回同一个实例来实现一个单例类：

```python
class Singleton(object):
    _instance = None
    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = object.__new__(cls, *args, **kwargs)

        return cls._instance

s1 = Singleton()
s2 = Singleton()
print(s1)
print(s2) 
#输出：
# <__main__.Singleton object at 0x000001AEC9403400>
# <__main__.Singleton object at 0x000001AEC9403400>
```
可以看到s1和s2都指向同一个对象，实现了单例模式。

再来看下工厂模式的实现
```python
class Fruit(object):
    def __init__(self):
        pass
    def print_color(self):
        pass

class Apple(Fruit):
    def __init__(self):
        pass
    def print_color(self):
        print("apple is in red")

class Orange(Fruit):
    def __init__(self):
        pass
    def print_color(self):
        print("orange is in orange")

class FruitFactory(object):
    fruits = {"apple": Apple, "orange": Orange}

    def __new__(cls, name):
        if name in cls.fruits.keys():
            return cls.fruits[name]()
        else:
            return Fruit()

fruit1 = FruitFactory("apple")
fruit2 = FruitFactory("orange")
fruit1.print_color()    
fruit2.print_color()    
fruit3 = FruitFactory("banana")
print(fruit2)
print(fruit3)

# 输出：
# apple is in red
# orange is in orange
# <__main__.Orange object at 0x000001AEC940F2E8>
# <__main__.Fruit object at 0x000001AEC94277F0>
```
另外一种使用场景是当你需要继承内置类时，例如int、str、tuple，只能通过__new__来达到初始化数据的效果：
```python
class g(float):
    """千克转克"""
    def __new__(cls, kg):
        return float.__new__(cls, kg * 2)
# 50千克转为克
a = g(50)
print(a)	# 100.0
print(a + 100)	# 200.0, 由于继承了float，所以可以直接运算，非常方便！
```

## `__del__`
这是析构方法，也就是在对象被垃圾回收之前被调用。

# 自定义序列和映射
序列和映射是对象的集合。为了实现它们的基本行为，如果对象是不可变的，就需要使用 `__len__` 和 `__getitem__` 两个魔法方法，如果是可变的，则还需要使用 `__setitem__` 和 `__delitem__` 。

`__len__(self)`

返回容器的长度，可变和不可变类型都需要实现。

`__getitem__(self, key)`

定义对容器中某一项使用 `self[key]` 的方式进行读取操作时的行为。这也是可变和不可变容器类型都需要实现的一个方法。它应该在键的类型错误式产生 TypeError 异常，同时在没有与键值相匹配的内容时产生 KeyError 异常。

`__setitem__(self, key)`

定义对容器中某一项使用 `self[key]` 的方式进行赋值操作时的行为。它是可变容器类型必须实现的一个方法，同样应该在合适的时候产生 KeyError 和 TypeError 异常。

`__delitem__(self, key)`

这个方法在对一部分对象使用 del 语句时被调用，同时必须删除和键相关的键。

# 访问控制

## `__str__`

当被 str() 调用时会执行__str__。此方法类似JAVA中的toString方法。

```python
class A:
    def __init__(self,name):
        self.name = name

class B:
    def __init__(self,name):
        self.name = name
    def __str__(self):#重写__str__方法
        return self.name

a = A("Tom")
print(a)
b = B("Jake")
print(b)
#输出：
#<__main__.A object at 0x000001AEC9447198>
#Jake
```

# 迭代器
## `__iter__`
该方法会返回一个迭代器。

迭代器是具有next方法的对象。在调用next方法时，会返回迭代器的下一个值。

除了在迭代器和可迭代对象上进行迭代外，还能把他们转换为序列。

下面的例子是用list构造方法显示地将迭代器转化为列表。

```python

```

# 生成器


# 参考
-   [Python面试之理解__new__和__init__的区别](https://juejin.im/post/5add4446f265da0b8d4186af)
-   

