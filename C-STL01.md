---
title: C++_STL01
typora-copy-images-to: C++_STL01
toc: true
date: 2021-10-28 10:55:46
tags:
index_img:
categories:
---

# Level0 认识C++标准库

- STL = 六大部件 = 80%C++标准库
- 参考网站：
  1. cplusplus.com
  2. cppreference.com
  3. Gcc.gnu.org

- STL六大部件（Components）：

  - 容器（Containers）

  - 分配器（Allocators）

  - 算法（Algorithms）

  - 迭代器（Iterators）：泛化的指针

  - 适配器（Adapters）

  - 仿函数（Functors）

    <img src="C++_STL01/image-20211028112653558.png" style="zoom:30%;" />



```c++
int main() {
  int ia[6] = {27, 210, 12, 47, 109, 83};
  vector<int, allocator<int>> vi(ia, ia+6);
  cout<< count_if(vi.begin(), vi.end(),
                 not1(bind2nd(less<int>(), 40)));
  return 0;
}
```

- `bind2nd`, `not1`都是适配器，将原来的less函数扩展成我们想要的样子

## 容器

