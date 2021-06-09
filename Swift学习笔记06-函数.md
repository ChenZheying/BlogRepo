---
title: Swift学习笔记06-函数
toc: true
categories: Swift学习
date: 2020-12-03 07:32:16
tags: [编程语言]
---
Swift函数学习笔记，包括[函数的定义与调用](#define)、[函数参数与返回值](#parameter)、[函数参数标签和参数名称](#label)、[函数类型](#type)、[嵌套函数](#nest)。
<!--more-->

<div id="define"></div>

## 函数的定义与调用

### 函数定义
一个函数需要：前缀`func`、**函数名**、函数的输入--**参数**（一个或多个）、函数的输出--**返回值类型**。

```swift
func greet(person: String) -> String {
    let greeting = "Hello, " + person + "!"
    return greeting
}
```

函数名为`greet`；参数有一个，标签是`person`，类型为`String`；返回值类型为`String`。

### 函数调用

按顺序在标签后传入相应实参
```swift
print(greet(person: "Anna"))
```

<div id="parameter"></div>

## 函数参数与返回值

### 无参数函数
```swift
func sayHelloWorld() -> String {
    return "hello, world"
}
print(sayHelloWorld())
```

### 多参数函数

括号中可以包括多个输入参数，以逗号分隔

```swift
func greet(person: String, alreadyGreeted: Bool) -> String {
    if alreadyGreeted {
        return greetAgain(person: person)
    } else {
        return greet(person: person)
    }
}
print(greet(person: "Tim", alreadyGreeted: true))
```

### 无返回值函数
可以不写`->`+返回类型以定义一个无返回值函数，自动返回一个在调用时可以忽略掉的`void`类型的空元组`()`

```swift
func greet(person: String) {
    print("Hello, \(person)!")
}
greet(person: "Dave")
```

### 多重返回值函数
利用元组将多个值组合成一个复合值从函数中返回。

```swift
func minMax(array: [Int]) -> (min: Int, max: Int) {
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if
value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
```

这个函数返回的是一个`(Int, Int)`类型的元组，第一个成员值命名为`min`，第二个成员值命名为`max`

```swift
let bounds = minMax(array: [8, -6, 2, 109, 3, 71])
print("min is \(bounds.min) and max is \(bounds.max)")
```

可以使用在返回值类型中定义的名称调用元组的成员值

### 可选元组返回类型
可将返回的元组的类型设置为可选类型，以表示函数返回的元组类型**有可能**整个元组都“没有值”。例如`(Int, Int)?`。  
`(Int, Int)?`与`(Int?, Int?)`不同，应注意区别。

```swift
func minMax(array: [Int]) -> (min: Int, max: Int)? {
    //利用可选返回类型处理空数组的情况
    if array.isEmpty { return nil }
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
```

### 隐式返回的函数
对于可以被写成一行`return`语句的函数,可以忽略不写`return`关键字。  
以下两个函数等价：

```swift
func greeting(for person: String) -> String {
    "Hello, " + person + "!"
}

func anotherGreeting(for person: String) -> String {
    return "Hello, " + person + "!"
}
```

<div id="label"></div>

## 函数参数标签和参数名称

每个函数参数都有一个参数标签以及一个参数名称。
```swift
func someFunction(argumentLabel parameterName: Int){
    //在函数体中用parameterName进行参数调用
    print(parameterName)
}
//调用函数时，在参数前应加上对应的标签
someFunction(argumentLabel: 123)
```

- 也可以省略不写参数标签，标签被默认和参数名称相同。
- 参数名不允许重复，标签名允许重复（建议不要重复）。
- 使用`_`作为一个参数的标签，表示传此参数时不需要加标签。

### 默认参数值
在定义函数时为某个参数设置一个默认值，在调用这个函数时就可以忽略这个参数。
```swift
func someFunction(parameterWithoutDefault: Int, parameterWithDefault: Int = 12) {
    // 如果你在调用时候不传第二个参数，parameterWithDefault 会值为 12 传入到函数体中。
}
```

### 可变参数
这里的可变是数量可变，一个可变参数可以接收零个或多个输入值。  
通过在变量类型名后面加入（`...`）的方式来定义可变参数。
```swift
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
// 返回 3.0, 是这 5 个数的平均数。
arithmeticMean(3, 8.25, 18.75)
// 返回 10.0, 是这 3 个数的平均数。
```

如上代码所示，`numbers`在函数体中被当成了一个`Double`型数组
- 一个函数最多只能拥有一个可变参数

### 输入输出参数
默认情况下，函数的参数被视为常量，无法更改参数值。解决办法：将参数定义为输入输出参数。  
定义方法：在参数前加上`inout`关键字。  
函数调用方法：传入的参数必须为变量，在参数名前加上`&`。
```swift
//包含输入输出参数的函数
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}

//函数调用
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
```

<div id="type"></div>

## 函数类型
函数的类型由函数的参数类型和返回类型组成。  
例如：
```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}
```

此函数类型为`(Int, Int) -> Int`
```swift

func printHelloWorld() {
    print("hello, world")
}
```
此函数的类型为`() -> Void`  

### 使用函数类型
可以像使用一个普通类型的常/变量一样使用函数类型的常/变量。
#### 变量赋值
     
```swift
var mathFunction: (Int, Int) -> Int = addTwoInts
/*也可以不指明常/变量的类型，让swift去推断*/
let anotherMathFunction = addTwoInts
```  
  
赋值之后可以mathFunction的类型变为`(Int, Int) -> Int`，调用它和调用`addTwoInts`函数是一样的  

#### 作为参数类型

```swift
func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
```

函数`printMathResult`的第一个参数是一个“参数是两个`Int`，返回值是一个`Int`”的函数。这个函数的作用是使用传入的函数，对传入的两个`Int`参数进行计算并打印结果。  
下面对函数的调用中，传入了函数`addTwoInts`和`3`和`5`。

<div id="returnFunc"> </div>

#### 作为返回类型  
在返回箭头（->）后写一个完整的函数类型，可以把函数作为另一个函数的返回类型。  
定义两个简单函数。

```swift
func stepForward(_ input: Int) -> Int {
    return input + 1
}
func stepBackward(_ input: Int) -> Int {
    return input - 1
}

//这两个函数作为另一个函数的返回值。
//根据传入的`Bool`参数的真假选择上面两个函数中的一个作为返回值。
func chooseStepFunction(backward: Bool) -> (Int) -> Int
{
    return backward ? stepBackward : stepForward
}


//调用函数，将得到的返回函数存储在一个常量中。
var currentValue = 3
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
 
//此时`moveNearerToZero`的类型为`(Int) -> Int`。  
//调用此函数。
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
```

<div id="nest"></div>

## 嵌套函数
可以把函数定义在别的函数体中，称作嵌套函数。嵌套函数是对外界不可见的，但是可以被它们的外围函数调用。  
使用嵌套函数的方法将上面将函数作为返回值类型的[示例](#returnFunc)进行重写。

```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int
{
    func stepForward(_ input: Int) -> Int {
           return input + 1
       }
    func stepBackward(_ input: Int) -> Int {
       return input - 1
    }
    return backward ? stepBackward : stepForward
}
```


