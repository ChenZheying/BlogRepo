---
title: Swift学习笔记08-枚举
date: 2020-12-05 09:05:16
tags: [编程语言]
toc: true
index_img: /img/swift.jpg
categories: Swift学习
---
Swift枚举学习笔记，包括[枚举语法](#grammar)、[关联值](#associatedValue)、[原始值](#originalValue)、[递归枚举](#recursiveEnum)。
<!--more-->

<div id="grammar"> </div>

## 枚举语法

### 枚举类型声明
```swift
enum SomeEnumeration {
    case casename1
    case casename2
    case casename3, casename4, casename5
}
```
可以换行定义一个新的`case`，也可以在同一行内定义多个`case`，用逗号隔开

### 枚举对象赋值

```swift
enum CompassPoint {
    case north
    case south
    case east
    case west
}

var directionToHead = CompassPoint.west
```
将`directionToHead`的值初始化为`CompassPoint`的成员值`west`，`directionToHead`的类型被推断为`CompassPoint`，在之后对`directionToHead`赋值时可直接使用点语法：
```swift
directionToHead = .east
```

### Switch匹配枚举值
枚举值可与`switch-case`语句配合使用
```swift
directionToHead = .south
switch directionToHead {
case .north:
    print("Lots of planets have a north")
case
 .south:
    print("Watch out for penguins")
case .east:
    print("Where the sun rises")
case .west:
    print("Where the skies are blue")
}
```  
与在其他情况下使用`switch-case`一样，`switch`必须穷举所有可能过的情况，也可以使用`default`涵盖未明确处理的情况。

### 枚举成员遍历
- 令枚举遵循`CaseIterable`协议  
    可以令枚举遵循`CaseIterable`协议，Swift会为它生成一个`allCases`属性，用于表示一个包含枚举所有成员的集合。

    ```swift
    enum Beverage: CaseIterable {
        case coffee, tea, juice
    }
    let numberOfChoices = Beverage.allCases.count
    print("\(numberOfChoices) beverages available")
    ```

- 用`for`循环遍历所有枚举成员  
    实质上就是一个集合的遍历
    ```swift
    for beverage in Beverage.allCases {
        print(beverage)
    }
    ```

<div id="associatedValue"> </div>

## 关联值
<div id = "relatedValue"> </div>

- 使用枚举的过程中，当需要把其他类型的值和成员值一起存储时，可将这些额外信息储存为关联值。
    ```swift
    enum Barcode {
        case upc(Int, Int, Int, Int)
    }
    var productBarcode = Barcode.upc(8, 85909, 51226, 3)
    ```  
    上例中的枚举类型`Barcode`有一个成员值`upc`，`upc`有一个`Int, Int, Int, Int`类型的关联值。  
    🗒️ Note：
    变量`productBarcode`的类型被推断为`Barcode`。

- 每个枚举成员的关联值类型可以各不相同

    ```swift
    enum Barcode {
        case upc(Int, Int, Int, Int)
        case qrCode(String)
    }
    var productBarcode = Barcode.upc(8, 85909, 51226, 3)
    productBarcode = .qrCode("ABCDEFGHIJKLMNOP")
    ```
    
    上例中的枚举类型`Barcode`有两个成员值：`upc`和`qrCode`，他们分别有`Int, Int, Int, Int`和`String`类型的关联值。  
    🗒️ Note：
    变量`productBarcode`在初始化为`Barcode`类型的值后，可以直接使用点语法将其赋值为该枚举类型的任一成员值（无论此成员值是否有关联值）。

### 在`switch-case`中提取关联值
可以将有关联值的`case`中关联值的各分量提取为单独的常量或变量。

#### 提取时区分常变量
可以为关联值中的各值分别考虑是提取为常量还是提取为变量。
```swift
switch productBarcode {
case .upc(let numberSystem, var manufacturer, let product, var check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case .qrCode(let productCode):
    print("QR code: \(productCode).")
}
```

#### 提取时令常变量统一
如果把关联值中的各值统一提取为常量（或变量），可在成员值前面加上常/变量声明关键字（即`let`或`var`），后面括号中的省略不写。
```swift
switch productBarcode {
case let .upc(numberSystem, manufacturer, product, check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case let .qrCode(productCode):
    print("QR code: \(productCode).")
}
```

<div id="originalValue"> </div>

## 原始值（rawValue）
原始值，即`rawValue`，需注意`rawValue`是不能改变的，和初值的概念区别很大。  
原始值是关联值的一种替代选择，是**给枚举类型成员值添加额外信息**的另一种方式。  
和关联值不同的是:  
- 原始值在定义枚举类型时被预先填充
- 原始值在设定好之后不能被改变
- 所有成员值的原始值必须拥有相同的类型
- 成员值和他的原始值有独特的一一对应的关系  

在为枚举类型定义[关联值](#relatedValue)时，在成员值后面加括号，里面写上关联值的类型；为枚举类型定义初始值时，在枚举类型名后加冒号，后面写上初始值的类型。

```swift
enum ASCIIControlCharacter: Character {
    case tab = "\t"
    case lineFeed = "\n"
    case carriageReturn = "\r"
}
```

在上例中枚举类型`ASCIIControlCharacter`的成员值的原始值都为`Character`类型。  

还可以[利用原始值进行枚举实例的初始化](#instanceInit)。

### 原始值的隐式赋值
原始值的隐式赋值指不显式地为每一个枚举成员设置原始值，由Swift自动进行赋值。  
Swift可对整数和字符串类型的原始值进行隐式赋值。

#### 整数类型原始值的隐式赋值
当原始值的类型为整数时，隐式赋值的值依次递增1。(如果第一个枚举成员没有设置原始值，其原始值将为0)
```swift
enum Planet: Int {
    case mercury = 2, venus, earth, mars, jupiter, saturn, uranus, neptune
    case test
}
var testValue = Planet.test
print(testValue.rawValue)
//打印10
```

上例中第一个枚举成员值的原始值被设置为整型 1 ，后面的成员值的原始值依次递增。

```swift
enum Planet: Int {
    case mercury, venus, earth, mars, jupiter, saturn, uranus, neptune
    case test
}
var testValue = Planet.test
print(testValue.rawValue)
//打印8
```

#### 字符串类型原始值的隐式赋值
当欲将原始值的类型设置为字符串时，成员值的原始值被隐式赋值为该成员值的名称。

```swift
enum CompassPoint: String {
    case north, south, east, west
}
var testValue = CompassPoint.north
print(testValue.rawValue)
//打印north
```

<div id = "instanceInit"> </div>

### 使用原始值初始化枚举实例

可以利用原始值和成员值一一对应的关系，利用成员值对应的原始值进行枚举实例的初始化：
```swift
enum Planet: Int {
    case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
}
let possiblePlanet = Planet(rawValue: 7)
// possiblePlanet 类型为 Planet? 值为 Planet.uranus
```

上例利用有原始值的枚举类型所带有的初始化方法，以rawValue为参数，由于不确定传入的rawValue参数是否能匹配到一个成员值，所以返回值类型为对应枚举类型的**可选类型**，值为对应的枚举成员值或`nil`。

<div id="recursiveEnum"> </div>

## 递归枚举
在为枚举成员设定关联值时，如果想将关联值的类型设定为本身的枚举类型，可以使用递归枚举。

### 为单独的成员值设置递归
在成员值前加上关键字`indirect`表示该成员可递归：
```swift
enum ArithmeticExpression {
    case number(Int)
    indirect case addition(ArithmeticExpression, ArithmeticExpression)
    indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

### 为所有成员设置递归
在枚举类型的声明前加上关键字`indirect`表示该枚举类型的所有成员都可递归：
```swift
indirect enum ArithmeticExpression {
    case number(Int)
    case addition(ArithmeticExpression, ArithmeticExpression)
    case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

### 枚举递归应用举例

利用递归可以更方便声明一个枚举实例用来表示表达式`(5 + 4) * 2 `：
```swift
let five = ArithmeticExpression.number(5)
let four = ArithmeticExpression.number(4)
let sum = ArithmeticExpression.addition(five, four)
let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))
```
利用递归函数进行上面表达式的求值
```swift
func evaluate(_ expression: ArithmeticExpression) -> Int {
    switch expression {
    case let .number(value):
        return value
    case let .addition(left, right):
        return evaluate(left) + evaluate(right)
    case let .multiplication(left, right):
        return evaluate(left) * evaluate(right)
    }
}
print(evaluate(product))
// 打印“18”
```