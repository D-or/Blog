---
title: Swift 之旅 —— 泛型
---

>   Swift 官方文档 泛型 学习笔记

### 泛型

#### 泛型函数

```swift
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
    let temporaryA = a
    a = b
    b = temporaryA
}

var someInt = 3
var anotherInt = 107
swapTwoValues(&someInt, &anotherInt)		// someInt 现在为 107，anotherInt 现在为 3

var someString = "hello"
var anotherString = "world"
swapTwoValues(&someString, &anotherString)		// someString 现在为 world，anotherString 现在为 hello
```

#### 类型参数

- **单个：<T>**
- **多个：<T, Q, P>**

#### 泛型类型

```swift
struct Stack<Element> {
    var items = [Element]()
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
}

var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")
stackOfStrings.push("cuatro")
// 现在栈中 ["uno", "dos", "tres", "cuatro"]

let fromTheTop = stackOfStrings.pop()		// fromTheTop 的值为 cuatro，现在栈中还有 ["uno", "dos", "tres"]
```

#### 扩展泛型类型

```swift
extension Stack {
    var topItem: Element? {
        return items.isEmpty ? nil : items[items.count - 1]
    }
}

if let topItem = stackOfStrings.topItem {
    print("The top item on the stack is \(topItem).")		// 输出 The top item on the stack is tres.
}
```

#### 类型约束

- **语法**

  ```swift
  func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
      // 泛型函数的函数体部分
  }
  ```

- **实例**

  ```swift
  func findIndex(ofString valueToFind: String, in array: [String]) -> Int? {
      for (index, value) in array.enumerated() {
          if value == valueToFind {
              return index
          }
      }
      return nil
  }

  let strings = ["cat", "dog", "llama", "parakeet", "terrapin"]

  if let foundIndex = findIndex(ofString: "llama", in: strings) {
      print("The index of llama is \(foundIndex)")		// 输出 The index of llama is 2
  }

  /*
  以下代码无法通过编译
  func findIndex<T>(of valueToFind: T, in array:[T]) -> Int? {
      for (index, value) in array.enumerated() {
          if value == valueToFind {
              return index
          }
      }
      return nil
  }
  */

  func findIndex<T: Equatable>(in array: [T], of valueToFind: T) -> Int? {
      for (index, value) in array.enumerated() {
          if value == valueToFind {
              return index
          }
      }
      return nil
  }

  let doubleIndex = findIndex(in: [3.14159, 0.1, 0.25], of: 9.3)		// doubleIndex 类型为 Int?，值为 nil

  let stringIndex = findIndex(in: ["Mike", "Malcolm", "Andrea"], of: "Andrea")		// stringIndex 类型为 Int?，值为 2
  ```

#### 关联类型

```swift
protocol Container {
    associatedtype ItemType
    mutating func append(item: ItemType)
    var count: Int { get }
    subscript(i: Int) -> ItemType { get }
}

struct IntStack: Container {
    // IntStack 的原始实现部分
    var items = [Int]()
    mutating func push(item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    
    // Container 协议的实现部分
    typealias ItemType = Int
    mutating func append(item: Int) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Int {
        return items[i]
    }
}

struct Stack<Element>: Container {
    // Stack<Element> 的原始实现部分
    var items = [Element]()
    mutating func push(item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
    
    // Container 协议的实现部分
    mutating func append(item: Element) {
    	self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Element {
        return items[i]
    }
}
```

#### 泛型 Where 语句

```swift
protocol Container {
    associatedtype ItemType
    mutating func append(item: ItemType)
    var count: Int { get }
    subscript(i: Int) -> ItemType { get }
}

extension Array: Container {}		// 使 Array 遵从 Container 协议

func allItemsMatch<C1: Container, C2: Container>
    (_ someContainer: C1, _ anotherContainer: C2) -> Bool
    where C1.ItemType == C2.ItemType, C1.ItemType: Equatable {
        
        // 检查两个容器含有相同数量的元素
        if someContainer.count != anotherContainer.count {
            return false
        }
        
        // 检查每一对元素是否相等
        for i in 0..<someContainer.count {
            if someContainer[i] != anotherContainer[i] {
                return false
            }
        }
        
        // 所有元素都匹配，返回 true
        return true
}

var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")

var arrayOfStrings = ["uno", "dos", "tres"]

if allItemsMatch(stackOfStrings, arrayOfStrings) {
    print("All items match.")
} else {
    print("Not all items match.")
}
// 输出 All items match.
```