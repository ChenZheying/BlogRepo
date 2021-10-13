---
title: C++Primer_04_处理类型
typora-copy-images-to: C++Primer_04
toc: true
date: 2021-09-03 14:17:09
tags:
index_img: /img/c++primer.jpg
categories: C++
---

# 类型别名

有两种方法可以定义类型别名：

1. 使用`typedef`关键字定义类型别名

2. 使用`using`进行别名声明

   ```cpp
   // 这两条语句等价
   typedef double wages;
   using wages = double;
   ```

## 指针、常量和类型别名

指针、const和typedef结合时，需格外注意是顶层const（指针是常量）还是底层const（指针指向常量）。

```cpp
typedef char *pstring;  //为char*定义一个别名pstring
const pstring cstr = 0; //cstr是一个指向char的常量指针
```

- 根据从右到左读的原则：
  1. `pstring cstr`：由于pstring是char*的别名，因此是定义一个指向char类型的指针`cstr`
  2. `const pstring cstr`：在一个指针变量之前加上一个const，变成一个指针常量（顶层const）。

- 📒Note注意：

  如果把pstring替换为其本来的名字char*，得到的语句是`const char *cstr = 0`，此时cstr是指向常量的指针（底层const），与其正确的含义不一致，应记住解析包含别名、const和指针的语句时，不要用替代别名的方法。

# auto类型说明符

c++11引入新的功能——`auto`类型说明符。因为c++是强类型的，所以在将表达式的值赋给变量时必须指明变量的类型，可以使用`auto`类型说明符，让编译器去分析其类型。

- 使用`auto`时，变量必须有初值

- 使用`auto`在一条语句中声明多个变量时，所有的变量必须具有一样的类型。

  ```cpp
  auto i = 0, *p = &i;
  ```

## 引用、指针、const和`auto`

`auto`和引用、指针、const结合时会有需要注意的东西。

### 引用和`auto`

```cpp
int i = 0, &r = i;
auto a = r;
```

在上面这段代码中，`r`是一个和int变量i绑定的引用，但使用`auto`推断a的类型时，不会把a推断成一个引用，会把a推断成`int`。

- 抽象这个点：用`auto`初始化对象时，如果等好友便是一个引用，`auto`会被推断为引用所绑定的变量的类型，而不是一个引用。

### const和`auto`

`auto`一般会忽略[顶层const](https://chenzheying.gitee.io/2021/07/29/C-Primer-03-const限定符/#const与指针)【指针（或变量）本身是常量】，保留[底层const](https://chenzheying.gitee.io/2021/07/29/C-Primer-03-const限定符/#const与指针)（指针指向常量）。



```cpp
const int ci = i, &cr = ci;
auto b = ci; 
auto e = &ci; 
```

- ci的const是**顶层const**，被忽略，b被声明为int类型。
- &ci是指针类型，指向常量ci，即带有**底层const**，所以e被声明为指向整数常量的指针。
