---
title: Swift FAQ
---

> 学习 **Swift** 过程中遇到的 Question 和 Answer.

#### 取模运算

```swift
// 下面的式子即为 oneNumber % anotherNunber
oneNumer.truncatingRemainder(dividingBy: anotherNumber)
```

#### mutating 在 class、 struct 及 extension 中的使用

```swift
protocol ExampleProtocol {
	mutating func exampleMethod()
}

class SimpleClass: ExampleProtocol {

	// class 不需要加关键字 mutating
	func exampleMethod() {
		// ...
	}
}

struct SimpleStruct: ExampleProtocol {

	// struct 必须加关键字 mutating
	mutating func exampleMethod() {
		// ...
	}
}

extension Int: ExampleProtocol {

	// extension 也必须加关键字 mutating
    mutating func exampleMethod() {
    	// ...
    }
}
```

#### 修改 let 声明的常量不报错

```swift
class SimpleClass {
    simpleDescription: String = ""
}

let test = SimpleClass()
test.simpleDescription = "Test"
```

#### 1 != true

```swift
let i = 1
if i {
    // 编译失败
}

let i = 1
if i == 1 {
    // 编译成功
}
```

#### 类型转换

```swift
let possibleNumber = "123"

let convertedNumber = Int(possibleNumber)		// convertedNumber 为 Int 可选类型
```

#### String 和 Character 类型之间的转换

```swift
for character in "Dog!🐶".characters {
    print(character)
}
// D
// o
// g
// !
// 🐶

let catCharacters: [Character] = ["C", "a", "t", "!", "🐱"]
let catString = String(catCharacters)
print(catString)		// Cat!🐱
```

