---
title: Swift 之旅 —— 闭包
---

>   Swift 官方文档 闭包 学习笔记

### 闭包

#### sorted(by:)

```swift
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]

func backward(_ one: String, _ another: String) -> Bool {
    return one > another
}
var reversedNames = names.sorted(by: backward)		// reversedNames 为 ["Ewa", "Daniella", "Chris", "Barry", "Alex"]
```

#### 闭包表达式

```swift
{(parameters) -> returnType in
    statements
}
```

```swift
// 基本用法
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
    return s1 > s2
})

// 短，可以写成一行
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in return s1 > s2 } )

// 根据上下文推断类型
reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )

// 单行表达式可以省略return
reversedNames = names.sorted(by: { s1, s2 in s1 > s2 } )

// 参数名称缩写
reversedNames = names.sorted(by: { $0 > $1 } )

// 使用运算符
reversedNames = names.sorted(by: >)
```

#### 尾随闭包

```swift
// 不使用尾随闭包
someFunctionThatTakesAClosure(closure: {
    // 闭包主体部分
})
 
// 当闭包为最后一个参数时，使用尾随闭包
someFunctionThatTakesAClosure() {
    // 闭包主体部分
}
```

```swift
reversedNames = names.sorted() { $0 > $1 }

// 若闭包是函数或方法的唯一参数，使用尾随闭包时，() 可省
reversedNames = names.sorted { $0 > $1 }
```

```swift
// map
let digitNames = [
    0: "Zero", 1: "One", 2: "Two",   3: "Three", 4: "Four",
    5: "Five", 6: "Six", 7: "Seven", 8: "Eight", 9: "Nine"
]
let numbers = [16, 58, 510]

let strings = numbers.map {(eachNumber) -> String in
    var number = eachNumber
    var output = ""
    repeat {
        output = digitNames[number % 10]! + output
        number /= 10
    } while number > 0
    return output
}
// strings 被推断为字符串类型数组，即 [String]，值为 ["OneSix", "FiveEight", "FiveOneZero"]
```

#### 值捕获

```swift
func makeIncrementer(forIncrement amount: Int) -> () -> Int {
    var runningTotal = 0
    func incrementer() -> Int {
        runningTotal += amount
        return runningTotal
    }
    return incrementer
}

let incrementByTen = makeIncrementer(forIncrement: 10)
incrementByTen()		// 返回的值为10
incrementByTen()		// 20
incrementByTen()		// 30

let incrementBySeven = makeIncrementer(forIncrement: 7)
incrementBySeven()		// 7
incrementByTen()		// 40
```

#### 闭包与函数都是引用类型

```swift
let alsoIncrementByTen = incrementByTen

alsoIncrementByTen()		// 返回的值为50
```

#### 逃逸闭包

```swift
var completionHandlers: [() -> Void] = []
func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
    completionHandlers.append(completionHandler)
}

func someFunctionWithNonescapingClosure(closure: () -> Void) {
    closure()
}

class SomeClass {
    var x = 10
    func doSomething() {
        someFunctionWithEscapingClosure { self.x = 100 }
        someFunctionWithNonescapingClosure { x = 200 }
    }
}

let instance = SomeClass()
instance.doSomething()		// instance.x 现在为 200

completionHandlers.first?()		// instance.x 现在为 100
```

#### 自动闭包

```swift
var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
print(customersInLine.count)		// 输出 5
 
let customerProvider = { customersInLine.remove(at: 0) }
print(customersInLine.count)		// 输出 5
 
print("Now serving \(customerProvider())!")		// 输出 "Now serving Chris!"
print(customersInLine.count)		// 输出 4
```

```swift
// customersInLine is ["Alex", "Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: () -> String) {
    print("Now serving \(customerProvider())!")
}
serve(customer: { customersInLine.remove(at: 0) }		// 输出 Now serving Alex!

// customersInLine is ["Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: @autoclosure () -> String) {
    print("Now serving \(customerProvider())!")
}
serve(customer: customersInLine.remove(at: 0))		// 输出 Now serving Ewa!

// customersInLine i= ["Barry", "Daniella"]
var customerProviders: [() -> String] = []
func collectCustomerProviders(_ customerProvider: @autoclosure @escaping () -> String) {
    customerProviders.append(customerProvider)
}
collectCustomerProviders(customersInLine.remove(at: 0))
collectCustomerProviders(customersInLine.remove(at: 0))
 
print("Collected \(customerProviders.count) closures.")		// 输出 "Collected 2 closures."
for customerProvider in customerProviders {
    print("Now serving \(customerProvider())!")
}
// 输出 "Now serving Barry!"
// 输出 "Now serving Daniella!
```

