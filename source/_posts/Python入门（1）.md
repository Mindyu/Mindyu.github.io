---
title: Python入门（1）
date: 2017-11-07 20:48:50
tags:  [Python]
categories: [Python学习]
---

### 自学[廖雪峰教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)代码笔记

通过自己在廖雪峰Python教程的学习，自己也尝试实现里面的一些基本语法，加强自己的理解，是学习一门语言最重要的部分！
Python是一种解释型、面向对象、动态数据类型的高级程序设计语言。
<!--more-->

``` stylus
# -*- coding: utf-8 -*-

L = [
    ['Apple', 'Google', 'Microsoft'],
    ['Java', 'Python', 'Ruby', 'PHP'],
    ['Adam', 'Bart', 'Lisa']
]
# 打印Apple:
print(L[0][0])
# 打印Python:
print(L[1][1])
# 打印Lisa:
print(L[2][2])
print('')  # python每次输出默认为一行，print('') 相当于换行

# 条件判断
height = 1.75
weight = 80.5

bmi = weight / (height * height)
if bmi < 18.5:
    print('过轻')
elif bmi < 25:
    print('正常')
elif bmi < 28:
    print('过重')
elif bmi < 32:
    print('肥胖')
else:
    print('严重肥胖')
print('')

# 循环计算高斯公式，range的使用
sum = 0
# for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
for x in range(11):  # 生成0-10的11个整数
    sum = sum + x
print(sum)
print('')

# List的使用
L = ['Bart', 'Lisa', 'Adam']
for x in L:
    print('Hello,', x)
print('')

# 循环
n = 1
while n <= 20:
    if n == 5:
        n = n + 1  # while循环，+1运算在循环尾，此处需要加1，才可保证
        continue
    if n > 10:  # 当n = 11时，条件满足，执行break语句
        break  # break语句会结束当前循环
    print(n)
    n = n + 1
print('END')
print('')

# list的特点：
# 1.查找和插入的时间随着元素的增加而增加；
# 2.占用空间小，浪费内存很少。

# dict（字典-相当于map）的特点：(以空间换时间)
# 1.查找和插入的速度极快，不会随着key的增加而变慢；
# 2.需要占用大量的内存，内存浪费多
d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
print(d['Bob'])
print('')

# set可以看成数学意义上的无序和无重复元素的集合
s = set([1, 1, 2, 2, 3, 3])  # 重复数据自动过滤掉
for x in s:
    print(x)
print('')

s = set((1, 2, 3))  # 将元组赋给set
for x in s:
    print(x)
print('')

# s = set((1,[2,3]))  # 不可以将tuple(不可变对象)赋给set
# for x in s:
#    print(x)
# print('')

# python内置函数

n1 = 255
n2 = 1000
print(hex(n1))
print(hex(n2))
print('')


def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x


x = -20
print(my_abs(x))
print('')


def power(x, n=2):  # x为必选参数，n为默认参数
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s


print(power(5))
print(power(5, 3))
print('')


def enroll(name, gender, age=6, city='Beijing'):
    print('name:', name)
    print('gender:', gender)
    print('age:', age)
    print('city:', city)


enroll('Yang', 'A', 18)
print('-------------')
enroll('Chen', 'B', city='wuhan')  # 当不按顺序提供部分默认参数时，需要把参数名写上
print('')


def add_end(L=[]):  # 默认参数L也是一个变量，它指向对象[]
    L.append('END')
    print(L)


add_end([1, 2, 3])
add_end()  # ['END']
add_end()  # ['END', 'END']

print('')


def add_end_1(L=None):  # 默认参数必须指向不变对象！  None为不变对象
    if L is None:
        L = []
    L.append('END')
    print(L)


add_end_1()
add_end_1()
print('')


# 可变参数  (可变参数在函数调用时自动组装为一个tuple元组)
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum


print(calc(1, 2, 3, 4))
print('')


# 关键字参数   （关键字参数在函数内部自动组装为一个dict字典） 作用：可以扩展函数的功能
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)


person('Adam', 45, gender='M', job='Engineer')
print('')

extra = {'city': 'Beijing', 'job': 'Engineer'}
person('Jack', 24, **extra)  # **kw关键字参数获取到extra字典（dict）的一个拷贝
print('')


# 必选参数、默认参数、可变参数、关键字参数和命名关键字参数的参数组合
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)


def f2(a, b, c=0, *, d, **kw):  # * 后面的参数被视为命名关键字参数
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)


f1(1, 2)
f1(1, 2, c=3)
f1(1, 2, 3, 'a', 'b')  # （'a', 'b'）可变参数
f1(1, 2, 3, 'a', 'b', x=99)
f2(1, 2, d=99, ext=None)
print('')


# 递归函数 与尾递归（在函数返回的时候，调用自身本身，并且return语句不能包含表达式）
# 计算n的阶乘
def fact(n):
    if n == 1:
        return 1
    return n * fact(n - 1)


print(fact(100))
print('')


# 上述递归中，如果调用次数过多就会出现栈溢出。 解决方法就是使用尾递归。
def fact_back(n):
    return fact_iter(n, 1)


def fact_iter(num, product):
    if num == 1:
        return product
    return fact_iter(num - 1, num * product)  # 每次递归时就把该次结果作为参数传递给下一轮循环


print(fact_back(100))
print('')

# 切片的使用
L = list(range(100))
print(L)
print('')
# 切片的三个参数的意义：1.首位置 2.末尾置 3.每n一取  [ )左闭右开区间
print(L[:10])
print(L[-10:])
print(L[10:20])
print(L[:10:2])
print(L[::5])
print('')

# 迭代器  可迭代对象有：字符串、list、tuple(元组)、dict、set
for ch in 'ABC':
    print(ch)
print('')

for i, value in enumerate(['A', 'B', 'C']):  # enumerate把一个list变成索引-元素对
    print(i, value)
print('')

for x, y in [(1, 1), (2, 4), (3, 9)]:  # 两个变量的迭代
    print(x, y)
print('')

# 列表生成器
print(list(range(1, 11)))  # 生成1-10的list，左闭右开
print('')

print([x * x for x in range(1, 11) if x % 2 == 0])  # 生成1-10之间偶数的平方
print('')

print([m + n for m in 'ABC' for n in 'XYZ'])  # 两层循环生成全排列
print('')

import os  # 导入os模块，模块的概念后面讲到

print([d for d in os.listdir('.')])  # [] 生成list
print('')

d = {'x': 'A', 'y': 'B', 'z': 'C'}  # dict
print([k + '=' + v for k, v in d.items()])  # 使用两个变量来生成list
print('')

L1 = ['Hello', 'World', 18, 'Apple', None]
print(L1)
print('')
L2 = [s.lower() for s in L1 if isinstance(s, str)]  # 如果s为字符串，则转换为小写生成列表
print(L2)
print('')

# 生成器：generator。 （用于一边循环一边推到下一个值）
g = (x * x for x in range(10))
print(g)  # 输出：<generator object <genexpr> at 0x0000009576A0A4C0>
#  next(g) 使用生成器生成下一个元素的值
print('')
for n in g:
    print(n)
print('')


def fib(max):  # 斐波纳契数列
    n, a, b = 0, 0, 1  # 变量初始化，相当于 n=0;a=0;b=1
    while n < max:
        print(b)
        a, b = b, a + b
        n = n + 1
    return 'done'


print(fib(6))
print('')


def fibonacci(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b  # 每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行
        a, b = b, a + b
        n = n + 1
    return 'done'


g = fibonacci(6)
print(next(g))
print(next(g))
print(next(g))
print(g)
print('')

while True:  # 必须捕获StopIteration错误，才可以获取到fibonacci（）的返回值
    try:
        x = next(g)
        print('g:', x)
    except StopIteration as e:
        print('Generator return value:', e.value)
        break
print('')

# 杨辉三角，生成器写法
def triangles():
    L = [1]
    while True:
        yield L
        L.insert(0, 0)    #首尾添加一个0，便于循环计算
        L.append(0)
        L = [L[i] + L[i + 1] for i in range(len(L)-1)]

n=0
for t in triangles():
    print(t)
    n = n + 1
    if n == 10:
        break
print('')

# 迭代器
# 可迭代对象（Iterable） （特点：可直接作用于for循环的）
# 1.集合数据类型，如list、tuple、dict、set、str等；
# 2.generator，包括生成器和带yield的generator function
# 迭代器（Iterator）对象：生成器
# list、tuple、dict、set、str可以通过iter()返回Iterator对象

from collections import Iterable
print(isinstance([], Iterable))
print('')

from collections import Iterator
print(isinstance([], Iterator))
print(isinstance(iter([]), Iterator))   

```

----------


身边有许多人都赶上AI学习的浪潮，我还是想努力做好一件事，在学习之余，自己看了看python，对这门语言很是好奇！很想先接触一下，虽然对python以后的方向并不是很清楚，适当扩展扩展自己的知识面也是灰常重要的！


