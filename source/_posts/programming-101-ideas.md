---
title: programming-101-ideas
date: 2023-07-07 11:32:59
tags:
---

下学期想给大一的同志们讲点入门课。在这里记录一点idea。

<!-- more -->

# 基于Java课进程的idea

## Programming -001: 如何学习？

-001 = ......999999

**如果你学编程只是为了完成主人的任务，那么会非常痛苦。不管你去什么系以后都要直接拿这玩意吃饭的！**

照着老师抄代码不可取。背代码更不可取。正经程序员的工作流程：我知道计算机能干A，B，C，D....这些事。现在来了一个需求，我通过思考，去分解这个需求，得到：我要实现这个功能，需要先B后C再A。ABC这三件事里，常见的事情我脑子里还记得该怎么写，毕竟我写了三四年代码。不常见的事情，我直接去上网查某某某功能该怎么写/用什么函数。

拿高中数学打个比方：我拿到一道概率大题。我知道这玩意先算概率再应用排列组合公式。概率太常见了，我会算。但是排列组合公式有点长，我忘了，我去查书，最后把这个做出来了。作为程序员不需要默写排列组合公式，只需要记得有一个公式能干什么活就行了。

每一步都要知道自己在干什么！！！！！！

寻根问底的精神。递归学习。

提问的智慧。

反例：报错->大佬这里怎么改->大佬这里怎么改->大佬这里怎么改->大佬这里怎么改->...  
正例：报错->为什么错->粗心->下次注意（如何不犯错？设计）
                 \\->这里不会->它是什么？->学会->以后也不会出错 

对“某某某老师没教”的回答：“不教你就不能会了？”  
高中不能对导数题进行一个洛，大学可没这限制。别人写仨小时作业，你用老师没教的牛逼方法半小时把作业秒了，那你牛逼，活该多玩两个半小时。
（当然，老师教的办法你也得会，不过学编程会了后面的前面的一般也会了）

Never use chatgpt。三点：
1. 熟练工。没代码量写不好代码。用一学期chatgpt等于高中抄一年数学作业，到头啥也做不出来。
2. 问chatgpt如何把大象放进冰箱。它会犯的错误：“首先把冰箱门打开，然后用车把大象装进去，把大象卸到地上，开走车，关上冰箱门。”冰箱 != 冷库。
3. 这玩意没有全局观。不会做design。实现一个功能还行，把功能组合起来不行。

## Programming 000：我们编程是为了什么/编程可以做什么

两方面：数据流（计算）+控制流（执行）。

数据流可以讲讲面向过程和lambda演算。一种输入x进入你的程序，经过f(g(h(j(k(x)))))得到输出y。”算法“的本质。

可以找一道oi题举例子。

控制流可以讲状态机。经典的小车寻线，甚至前进转弯到达指定地点。

编程本身：以规定好的语法组合一系列规定好的符号去描述上述的过程。举例：解数学题。

基础正则表达式教学。

## Programming 001：计算机组成原理（如何计算）

逻辑门、CPU、内存、基础那几条汇编。

这些讲一节课够了。

## Programming 002: code！

代码风格，ctrl+alt+L，变量命名规范，重复功能抽成函数。

如何看报错，常见报错，如何搜索。提问的智慧。

基本输入输出（抄下来即可），类型系统，运算符，作用域。

## Programming 003: memory

java内存管理。堆栈。ref type。

函数，call stack。

## Programming 004: 基础算法和数据结构（如何组织数据）

数组，arraylist

冒泡，递推。

## Programming 005, 006: 范式（如何复用代码、记起自己几天前写了什么，快速知道别人写了什么）
面向过程，面向对象。类，接口，开闭原则，is-a。为什么要面向对象，面向对象的一些用例（功能替换）。

很重要的：经验。

## Programming 007：project design
我的blog。git。
