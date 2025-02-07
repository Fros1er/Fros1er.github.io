---
title: 一些c艹碎碎念
date: 2023-01-28 14:31:33
tags: 
---
记录一些c艹问题。

## rvalue ref是左值

下面写的不太对，重新研究了一下。

从语义上讲，rvalue reference的变量是一个hint...提示程序员稍后可以move这个变量。这个用法其实不常见。

更常见的用法是在泛型里。`for (auto&& e : container)`的`e`可以装任何东西，无论迭代器返回的是prvalue(`T`), lvalue ref(`T &`)还是xvalue(`T &&`)。e后面传给函数的时候有时候接一个forward。

其他时候`T &&x;`一点用没有，有时候甚至比`T x`还要烂。
```
T &&x = move(y);
return move(x)
```
调用两次move ctor，而`T x`在优化copy的情况下只调用一次。

----

参透了一切...

xvalue几乎是人为规定的。一个value以后弃用了，但还没到destory的时候可以称为xvalue。

那么，xvalue作为一个左值，我们希望编译器把它当作右值看，回收掉有用的部分。

How？`reinterpret_cast<T &&>()`。

所以rvalue ref是一个左值，表明你这个scope里持有这个对象的所有权。但是，它在给人一个提示：它应该过会被当作右值处理，外面的东西已经不管它了。

还可以从生命周期的角度...只要把move当作关键字看，一切都合理了起来。
``` cpp
T &&rv = ...; //for example...
func(rv); // rv is not implicily moved into func
rv.xxx; // still available!
func(std::move(rv)); // only move transfer ownership
```

## 生命周期延长
在出一个函数（还有new）之前，把rvalue传给`const T&`和`T&&`，它暂时不会析构。  
注意如果有`class X{ const T& t; };`，`X x{{1}}`没啥问题，`X *x = new X{{1}}`会dangling ref。

<https://en.cppreference.com/w/cpp/language/reference_initialization#Lifetime_of_a_temporary>

## const ref vs move?
<https://stackoverflow.com/questions/63466914/move-semantics-vs-const-reference?noredirect=1&lq=1>
``` cpp
class X {
    std::string s;
  public:
    explicit X(const std::string &s) : s{s} {} //or
    explicit X(std::string &&s) : s{std::move(s)} {} //or
    explicit X(std::string s) : s{std::move(s)} {} //or
    template<typename T>
    explicit X(T&& s) : s(std::forward<T>(s)) {}
}
```
有几个问题：
1. 哪个避免拷贝？
2. rvalue不能直接传const ref. 如何解决？

好像是(3)比较常用...

还没实际测过。
