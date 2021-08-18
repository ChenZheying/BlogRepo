---
layout: post
title: Swift学习笔记12-下标
date: 2021-04-14 07:36:41
tags: [编程语言]
categories: Swift学习
index_img: /img/swift.jpg
toc: true
---
Swift下标学习笔记，包括[下标语法](#下标语法)和[类型下标](#类型下标)。
<!--more-->

下标可以定义在类、结构体和枚举中，是访问集合、列表或序列中元素的快捷方式。（可以将下标理解为一个用特殊关键字（`subscript`）声明的，必须有返回值的特殊方法）
- 数组（Array）、字典（Dictionary）默认有下标。
- [使用下标访问字符串（String）中的字符](https://chenzheying.gitee.io/2020/11/24/Swift学习笔记03-字符串和字符/#访问和修改字符串)时，传入方括号的参数应为`index`类型。  

  
  
## 下标语法

### 下标声明
- 使用`subscript`关键字在一个类型中声明下标。
- 使用`get`关键字定义下标取值的方法。（类似[计算属性中的get](https://chenzheying.gitee.io/2020/12/13/Swift学习笔记10-属性/#计算属性)）
- 使用`set`关键字定义下标赋值的方法。（类似[计算属性中的set](https://chenzheying.gitee.io/2020/12/13/Swift学习笔记10-属性/#计算属性)）

```swift
struct SomeType{
    subscript(index: Int) -> Int {
        get {
          // 返回一个适当的 Int 类型的值
        }
        set(newValue) {
          // 执行适当的赋值操作
        }
    }
}
```

与计算属性类似：
- 可以[不指定setter的参数](https://chenzheying.gitee.io/2020/12/13/Swift学习笔记10-属性/#简化Setter声明)，默认名称为`newValue`。
- 可以[省略get关键字和对应的大括号](https://chenzheying.gitee.io/2020/12/13/Swift学习笔记10-属性/#简化Getter声明)。
- 可以不设置`Setter`以得到一个[只读下标](https://chenzheying.gitee.io/2020/12/13/Swift学习笔记10-属性/#只读计算属性)。

### 下标使用

#### 使用下标取值

```swift
var numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]

print(numberOfLegs["spider"])
```
通过下标返回的是一个`Int?`，因为不是每个键都有对应的值。
#### 使用下标赋值

```swift
var numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]

numberOfLegs["bird"] = 2
```

- 可以给已在字典中的键赋值，也可以指定一个新的键进行赋值。
- 可将键对应的值赋值为`nil`以实现删除键对应值

#### 下标重载
一个类或结构体可以根据自身需要提供多个下标实现，使用下标时将通过入参的数量和类型进行区分，自动匹配合适的下标。它通常被称为下标的重载。
```swift
struct Matrix {

    let rows: Int, columns: Int

    var grid: [Double]

    init(rows: Int, columns: Int) {

        self.rows = rows

        self.columns = columns

        grid = Array(repeating: 0.0, count: rows * columns)

    }
    func indexIsValid(row: Int, column: Int) -> Bool {

        return row >= 0 && row < rows && column >= 0 && column < columns

    }

    subscript(row: Int, column: Int) -> Double {

        get {

            assert(indexIsValid(row: row, column: column), "Index out of range")

            return grid[(row * columns) + column]

        }

        set {

            assert(indexIsValid(row: row, column: column), "Index out of range")

            grid[(row * columns) + column] = newValue

        }

    }

}
```

## 类型下标

与[类型属性](https://chenzheying.gitee.io/2020/12/13/Swift学习笔记10-属性/#类型属性)和[类型方法](https://chenzheying.gitee.io/2021/03/10/Swift学习笔记11-方法/#类型方法)类似：
- 类型下标不属于实例，而属于一个类型。
- 使用`static`定义子类**不**可重写的类型下标，使用`class`定义子类可重写的类型下标。


```swift
enum Planet: Int {

    case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune

    static subscript(n: Int) -> Planet {

        return Planet(rawValue: n)!

    }

}

let mars = Planet[4]

print(mars)

```