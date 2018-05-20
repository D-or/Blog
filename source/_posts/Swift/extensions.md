---
title: Swift 之旅 —— 扩展
---

>   swift 官方文档 扩展 学习笔记

### 扩展

#### 语法

```swift
extension SomeType {
    // 为 SomeType 添加的新功能
}

extension SomeType: SomeProtocol, AnotherProctocol {
    // 协议的实现
}
```

#### 计算型属性

```swift
extension Double {
    var km: Double { return self * 1_000.0 }
    var m : Double { return self }
    var cm: Double { return self / 100.0 }
    var mm: Double { return self / 1_000.0 }
    var ft: Double { return self / 3.28084 }
}

let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")		// 输出 One inch is 0.0254 meters

let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")		// 输出 Three feet is 0.914399970739201 meters

let aMarathon = 42.km + 195.m
print("A marathon is \(aMarathon) meters long")		// 输出 A marathon is 42195.0 meters long
```

#### 构造器

```swift
struct Size {
    var width = 0.0, height = 0.0
}

struct Point {
    var x = 0.0, y = 0.0
}

struct Rect {
    var origin = Point()
    var size = Size()
}

let defaultRect = Rect()
let oneRect = Rect(origin: Point(x: 4.0, y: 4.0), size: Size(width: 3.0, height: 3.0))
// anotherRect 的原点是 (4.0, 4.0)，大小是 (3.0, 3.0)

extension Rect {
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}

let anotherRect = Rect(center: Point(x: 4.0, y: 4.0), size: Size(width: 3.0, height: 3.0))
// anotherRect 的原点是 (2.5, 2.5)，大小是 (3.0, 3.0)
```

#### 添加方法

```swift
extension Int {
    func repetitions(_ task: () -> Void) {
        for _ in 0 ..< self {
            task()
        }
    }
}

3.repetitions( { print("Hello!") } )
// Hello!
// Hello!
// Hello!

//使用尾随闭包
3.repetitions{ print("Goodbye!") }
// Goodbye!
// Goodbye!
// Goodbye!
```

- **可变实例方法**

  ```swift
  extension Int {
      mutating func square() {
          self = self * self
      }
  }

  var someInt = 3
  someInt.square()		// someInt 现在为 9
  ```

#### 添加新下标

```swift
extension Int {
    subscript(digitIndex: Int) -> Int {
        var decimalBase = 1
        for _ in 0..<digitIndex {
            decimalBase *= 10
        }
        return (self / decimalBase) % 10
    }
}

746381295[0]		// 返回 5

746381295[1]		// 返回 9

746381295[2]		// 返回 2

746381295[8]		// 返回 7
```

#### 嵌套类型

```swift
extension Int {
    enum Kind {
        case Negative, Zero, Positive
    }
    var kind: Kind {
        switch self {
        case 0:
            return .Zero
        case let x where x > 0:
            return .Positive
        default:
            return .Negative
        }
    }
}

func printIntegerKinds(_ numbers: [Int]) {
    for number in numbers {
        switch number.kind {
        case .Negative:
            print("- ", terminator: "")
        case .Zero:
            print("0 ", terminator: "")
        case .Positive:
            print("+ ", terminator: "")
        }
    }
    print("") // *.*
}

printIntegerKinds([3, 19, -27, 0, -6, 0, 7])		// 输出 + + - 0 - 0 +
```