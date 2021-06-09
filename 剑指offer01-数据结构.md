---
layout: post
title: 剑指offer01-数据结构
date: 2021-03-08 20:39:26
tags: 面试题
categories: 算法
toc: true
---
# 数组
数组的特点：
1. 容量大小固定 --> 数组的空间效率不是很好，经常会有空闲区域不被充分利用
2. 内存连续 --> 数组的时间效率比较高，可实现简单的哈希表

## 面试题3：二维数组中的查找
```
在一个的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。
如下面的二维数组，如果查找数字7，返回 true；如果查找数字5，返回 false。

1   2   8   9
2   4   9   12
4   7   10  13
6   8   11  15
```
书中给出的思路：  
从矩阵右上角的数字开始查找：  
1. 若此数字大于目标数字，目标数字在此数字的左边或上边，删除掉此列继续查找
2. 若此数字等于目标数字，返回true
3. 若此数字小于目标数字，目标数字在此数字的右边或下边，由于此数字右边的列都被删除，故目标数字在此数字的下边，删除此行继续查找
4. 若此数字是矩阵左下角的数字且不等于目标数字，返回false

swift版本的代码如下
```swift
func Find(matrix: Array<Array<Int>>, rows: Int, columns: Int, numberToFind: Int) -> Bool {
    var found: Bool = false
    // 第一步应该判断输入是否合法
    if !matrix.isEmpty && rows > 0 && columns > 0 {
        var row: Int = 0
        var column: Int = columns - 1
        var currentNum: Int
        while row < rows && column >= 0 {
            currentNum = matrix[row][column]
            // print("position (\(row), \(column))")
            if currentNum == numberToFind
            {
                found = true
                break
            }
            else if currentNum > numberToFind
            {
                column -= 1
            }
            else if currentNum < numberToFind
            {
                row += 1
            }
        }
    }
    return found
}

var someInts = [[Int]]()
someInts = [
    [1, 2, 8, 9],
    [2, 4, 9, 12],
    [4, 7, 10, 13],
    [6, 8, 11, 15]
]

let rows = 4, columns = 4, number = 7

print(Find(matrix: someInts, rows: rows, columns: columns, numberToFind: number))

```

改进：
1. swift中的数组（Array）对应的是c++中的vector，故可使用``.count``来得到矩阵的行数和列数
2. 使用断言和先决条件而不是简单的if语句进行输入合法性判断

```swift
func FindInPartiallySortedMatrix(matrix: Array<Array<Int>>, numberToFind: Int) -> Bool {
    var found: Bool = false
    // 第一步应该判断输入是否合法
    if matrix.isEmpty {
        assertionFailure("输入的矩阵不可为空")
    }

    let rows = matrix.count, columns = matrix.first!.count
    var row: Int = 0
    var column: Int = columns - 1
    var currentNum: Int
    while row < rows && column >= 0 {
        currentNum = matrix[row][column]
        // print("position (\(row), \(column))")
        if currentNum == numberToFind
        {
            found = true
            break
        }
        else if currentNum > numberToFind
        {
            column -= 1
        }
        else if currentNum < numberToFind
        {
            row += 1
        }
    }
    
    return found
}

var someInts = [[Int]]()
someInts = [
    [1, 2, 8, 9],
    [2, 4, 9, 12],
    [4, 7, 10, 13],
    [6, 8, 11, 15]
]
let number = 7
print(FindInPartiallySortedMatrix(matrix: someInts, numberToFind: number))

```

# 字符串

## 面试题4:替换空格

```
请实现一个函数，把字符串 s 中的每个空格替换成"%20"。

示例 1：

输入：s = "We are happy."
输出："We%20are%20happy."
```

- 我的思路：  
    1. 先遍历一遍，得到空格的个数`blankCount`
    2. 对原字符串进行扩容，增加`blackCount * 2`个位置
    3. 对原字符进行逆序遍历并进行判断：
       1. 如果当前字符不是空格：  
        将此字符值赋给它之后`blankCount * 2个`位置的字符；
       2. 如果当前字符是空格：  
        将此字符之后`(blankCount-1)*2`、`(blankCount-1)*2+1`、`(blankCount-1)*2+2`位置的三个字符分别赋值为`%`、`2`、`0`；
       3. 当`blankCount`为0时，返回当前字符串。

- 使用Swift语言进行算法设计：  
一开始准备使用`inout`参数编写一个无返回值的函数，进行对所输入`string`的修改，但是swift中无法通过索引的方式对字符串的指定位置的字符进行赋值操作。  
故在函数中先将输入的`string`存储在一个`Array`中，并在此基础上进行算法设计。
```swift
func ReplaceBlank(aString: String) -> String {
    //将String保存在一个数组中
    var stringArray = Array(aString)
    //函数的第一步检查传入参数是否合法
    if stringArray.isEmpty {
        assertionFailure("输入字符串不可为空！")
    }
    let originStringLength = stringArray.count
    //用一个变量记录当前遍历到的空格数目
    var blankCount = 0
    //循环1:统计空格的数目
    for char in stringArray{
        if char == " " {
            blankCount += 1
        }
    }
    if blankCount == 0 {
        return aString
    }
    //增长string
    stringArray = stringArray + Array(repeating: " ", count: blankCount*2)
    //循环2:移动字符串中的字符
    for index in (0...originStringLength-1).reversed(){
        //判断：如果当前字符不是空格，则将此字符值赋给它之后blankCount*2个位置的字符；
        //如果是空格，blankCount-1，同时将它之后(blankCount-1)*2、(blankCount-1)*2+1、(blankCount-1)*2+2位置的三个字符分别赋值为%、2、0；
        //当blankCount为0时，函数返回
        if stringArray[index] == " " {
            stringArray[index + (blankCount-1)*2] = "%"
            stringArray[index + (blankCount-1)*2 + 1] = "2"
            stringArray[index + (blankCount-1)*2 + 2] = "0"
            blankCount-=1
        }
        else{
            stringArray[index + blankCount * 2] = stringArray[index]
        }
        if blankCount == 0 {
            break
        }
    }
    return String(stringArray)
}
var a = "We are happy. How about you?"
print(ReplaceBlank(aString: a))
```

实际上，swift的`Foundation`库中有现成的替换字符串中所有特定字符的函数  
```swift
import Foundation
var a = "We are happy. How about you?"
a = a.replacingOccurrences(of: " ", with: "%20")
print(a)
```

- 书中给出的思路：  
在为原字符串增加完长度后，使用两个指针分别进行位置标记。  
一个指针指向原字符串的末尾字符，从后往前检查哪些字符需要替换；另一个指针指向增长后的字符串的末尾，标记住新字符应该填充的位置。
```swift
    //循环2:使用两个指针进行检查和修改操作
    var originIndex = originStringLength-1
    var newIndex = stringArray.count-1
    while(originIndex >= 0 && newIndex > originIndex){
        if stringArray[originIndex] == " " {
            stringArray[newIndex] = "0"
            newIndex -= 1
            stringArray[newIndex] = "2"
            newIndex -= 1
            stringArray[newIndex] = "%"
            newIndex -= 1
        }
        else{
            stringArray[newIndex] = stringArray[originIndex]
            newIndex -= 1
        }
        originIndex -= 1
    }
```

