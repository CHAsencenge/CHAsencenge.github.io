---
title: python yield
date: 2019-07-25 14:59:22
tags: 
- python
categories:
- python
---
<blockquote class="blockquote-center">python中yield关键字的用法
</blockquote>
<!--more-->

## 迭代(iteration)与可迭代(iterable)
使用__容器__时逐个获取元素的过程为__迭代__。

## 哪些类型是可迭代的

- python中的顺序类型： `list`, `tuple`(元组，列表可修改元组不可修改，列表用中括号元组用小括号), `string`.
- `dict`, `set`, `file`.
- 某类对象提供了 `__iter__()` 或者 `__getitem__()` 方法.

## 迭代器
对迭代器不断调用`next()`方法，可依次获取下一个元素，迭代器`__iter__()`方法返回迭代器自身，因此迭代器也是可迭代的。

## 迭代器协议(iterator protocol)
一个容器提供`__iter__()`方法，该方法能返回一个能逐个访问容器内所有元素的迭代器，则该容器实现了迭代器协议。

### python处理for循环的过程
```python
for x in something:
	print(x)
```
处理for循环首先调用内建函数`iter(something)`,内建函数调用`something.__iter__()`,返回something对应的迭代器，然后for循环会调用内建函数`next()`，作用在迭代器上获取迭代器的下一个元素，并赋值给x

## 生成器函数(generaor function)和生成器(generator)
如果一个函数包含 yield 表达式，那么它是一个生成器函数；调用它会返回一个特殊的迭代器，称为生成器。

生成器函数被调用后，其函数体内的代码并不会立即执行，而是返回一个生成器（generator-iterator）。当返回的生成器调用成员方法时，相应的生成器函数中的代码才会执行。

## “下一个yield表达式”
调用 generator.next() 时，生成器函数会从当前位置开始执行到下一个 yield 表达式。这里的「下一个」指的是执行逻辑的下一个。

```python
def f123():
    yield 1
    yield 2
    yield 3

for item in f123(): # 1, 2, and 3, will be printed
    print(item)

def f13():
    yield 1
    while False:
        yield 2
    yield 3

for item in f13(): # 1 and 3, will be printed
    print(item)
```
### 使用 send() 方法与生成器函数通信

```python
def func():
    x = 1
    while True:
        y = (yield x)
        x += y

geniter = func()
geniter.next()  # 1
geniter.send(3) # 4
geniter.send(10)# 14
```
生成器函数 func 用 yield 表达式，将处理好的 x 发送给生成器的调用者；与此同时，生成器的调用者通过 send 函数，将外部信息作为生成器函数内部的 yield 表达式的值，保存在 y 当中，并参与后续的处理。

## yield的好处
顺序访问容器内的前五个元素：

- way1:获取所有元素然后取前五
- way2:逐个迭代，至第五个元素

假设对于一个func(),返回值为列表，调用者对其返回值只有逐个迭代：

- 若等函数生成所有元素可能需要很长时间
- 使用yield把func()变成一个生成器函数，每次产生一个元素，可以节省开销
