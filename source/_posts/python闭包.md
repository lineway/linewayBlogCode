---
title: python闭包
date: 2018-05-23 21:30:13
tags: python
categories: 编程学习
---

## 关于闭包
　　闭包(closure)的概念多见于javascript语言中，实际上，python语言中也存在闭包的概念，但是往往没有被注意到，实际上，在定义一个装饰器的时候，就用到了闭包的概念。  
　　关于闭包，实现起来只需要注意三点就好：  
　　- 嵌套函数；  
　　- 内部函数用到了外部变量；  
　　- 外部函数返回内部函数；  
关于闭包的概念就像上述这么简单，但是该怎么实现一个闭包函数呢？接着往下看。

## 实现闭包函数
这是一个简单的示例：

```python
def compute(x):
    def echo(value):
        return value*x
    return echo
```
这是一个简单的闭包函数，但是已经符合上面提及到的条件。  
 
 1. 在外层函数`compute`中嵌套了一个内层函数`echo`；
 2. 内层函数的返回值中调用到外层函数的参数`x`;
 3. 外部函数`compute`返回内层函数`echo`;

那么我们直接调用外层函数，输出结果是什么呢？  

```python
def compute(x):
    def echo(value):
        return value * x
    return echo

print(compute(2))
```
输出结果是：  

```python
<function compute.<locals>.echo at 0x107d1a730>
```
看得出来，虽然内层函数没有传递参数，但是结果并没有报错，返回的是一个函数对象，这也符合上述第三点所说的，**外部函数返回内部函数**这一个条件。  
那么怎么才能使用这么闭包函数呢？既然我们知道了外部函数返回的是内部函数，那么按照python中一切皆对象的哲学，可以试一试：  

```python
func = compute(2)
print(func(3))
```
这一次，有输出结果`6`，也符合我们函数的逻辑返回`x=2,value=3`的值。这一步，我们实现了一个简单的闭包函数。  

## 关于闭包函数
### 闭包函数的参数
首先，我们讨论一下闭包函数的变量作用域，看下面的一个示例：  

```python
def foo(x):
    b = 5
    def bar(y):
        return x*b+y
    return bar

b = 10
func = foo(2)
print(func(4))
```
这个示例的输出是`14`，而不是`24`。造成这个结果的原因是：**闭包函数中传入的参数是在定义的时候确定的，而不是在调用的时候**，即`print(func(4))`这行代码在运行的时候，其中传入的参数`b`是在函数`foo`定义的时候传入的参数`5`。  
### 关于`__closure__`
在python中，函数也是对象，那么函数也有属性，其中和闭包相关的属性就是`__closure__`。`__closure__`属性定义了一组包含`cell`对象的元组，该元组中的每一个`cell`用来保存作用域中变量的值。  

```python
def foo(x):
    b = 5
    def bar(y):
        return x*b+y
    return bar

func = foo(2)
print(func.__closure__)
print(len(func.__closure__))
print(func.__closure__[0].cell_contents)
print(func.__closure__[1].cell_contents)
```
上述代码输出的结果如下：  

```python
(<cell at 0x10f9bf078: int object at 0x10f7b3ac0>, <cell at 0x10f9bf1c8: int object at 0x10f7b3a60>)
2
5
2
```
其中的含义分别是，`__closure__`属性中存在两个`cell`，则说明在作用域中有两个变量值的存在，`print(len(foo.__closure__))`的返回值是`2`也说明了这一点。两个变量值分别是`5`和`2`,对应的也就是定义中的`b`的值和`func=foo(2)`中传入的参数值。

## 闭包的使用
接触过python的人都知道，python语言中有装饰器这个语法糖，实际上，编写一个装饰器的时候，就用到了闭包相关的概念。例如，《Python Cookbook》中的一个示例：

```python
import time
from functools import wraps

def timethis(func):
    '''
    Decorator that reports the execution time.
    '''
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(func.__name__, end-start)
        return result
    return wrapper
```
主体函数中，使用的就是闭包的概念。理解了闭包的概念，对于装饰器的理解会更好一些，后续在关于装饰器的使用给出一些总结。
