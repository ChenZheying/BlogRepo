---
layout: post
title: Swift学习笔记13-继承
date: 2021-04-15 09:51:04
tags: [编程语言]
categories: Swift学习
toc: true
---
Swift继承学习笔记，包括[基类](#基类)和[重写](#重写)。
<!--more-->

- 一个类可以继承另一个类的方法，属性和其它特性。
- 继承是区分「类」与其它类型的一个基本特征。

## 基类
任何不继承于其它类的类，都是基类。

```swift
class Vehicle {

    var currentSpeed = 0.0

    var description: String {

        return "traveling at \(currentSpeed) miles per hour"

    }

    func makeNoise() {

        // 什么也不做——因为车辆不一定会有噪音

    }

}
```

基类也可以创建实例，调用

```swift
let someVehicle = Vehicle()
print("Vehicle: \(someVehicle.description)")
```

在很多情况下，基类具有通用特性，但它的实例不够具体，因此用多个描述具体类型的子类继承这个基类。

## 子类
子类不一定非要继承自一个基类，也就是说，子类可以继承自一个子类。

```swift
class Bicycle: Vehicle {

    var hasBasket = false

}
class Tandem: Bicycle {

    var currentNumberOfPassengers = 0

}
```

- 子类`Bicycle`继承自基类`Vehicle`，继承了`Vehicle`的存储属性`currentSpeed`、[只读计算属性](https://chenzheying.gitee.io/2020/12/13/Swift学习笔记10-属性/#只读计算属性)`description`和方法`makeNoise`，有自己的新的存储属性`hasBasket`。
- 子类`Tandem`继承自子类`Bicycle`，继承了`Bicycle`从`Vehicle`继承来的存储属性`currentSpeed`、只读计算属性`description`和方法`makeNoise`，还继承了`Bicycle`的存储属性`hasBasket`，有自己的新存储属性`currentNumberOfPassengers`。

## 重写

可以使用`override`关键字给子类继承来的实例方法，类方法，实例属性，类属性或下标，提供自己定制的实现。

### 访问超类（父类）的资源

- 访问超类（父类）的方法：`super.someMethod()`
- 访问超类（父类）的属性：`super.someProperty()`
- 访问超类（父类）的下标：`super[someIndex]`

### 重写方法

```swift
class Train: Vehicle {

    override func makeNoise() {

        print("Choo Choo")

    }

}
```

使用`Train`的实例调用`makeNoise`方法时，会执行重写后的方法。

### 重写属性

- 实例属性和类型属性都可以被重写。
- 对属性的重写分为
    1. 重写属性的`Getters`和`Setters`。
    2. 为重写属性添加属性观察器。
   
   两种重写不可同时存在。

#### 重写属性的`Getters`和`Setters`

```swift
class Car: Vehicle {

    var gear = 1

    override var description: String {

        return super.description + " in gear \(gear)"

    }

}
```

🗒️ Note：
- 可以讲继承来的只读属性重写为读写属性，不可以讲继承来的读写属性重写为只读属性。
- 重写属性中可以只有`getter`，不可以只有`setter`。

#### 为重写属性添加属性观察器
```swift
class AutomaticCar: Car {

    override var currentSpeed: Double {

        didSet {

            gear = Int(currentSpeed / 10.0) + 1

        }

    }

}

```

🗒️ Note：
- 继承来的**常量存储型属性**或继承来的**只读计算型属性**不可以添加属性观察器。
- 不可以同时提供重写的`setter`和重写的属性观察器。（因为可以在`setter`中实现属性观察器的内容）

### 防止重写

可以使用`final`关键字声明某个类、方法、属性或下标是不可重写的。
```swift
final class SomeClass { }

final var someVar

final func someFunc(){}

final subscript

```