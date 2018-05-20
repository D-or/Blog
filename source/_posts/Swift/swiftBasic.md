---
title: Swift 之旅 —— Basic
---

>   Swift 官方文档 Basic 学习笔记

## Basic

### 常量和变量

#### 声明

- 常量：let

  ```swift
  let Length = 10		// 默认类型为 Int

  let a = 0.0, b = 1.0, c = 2.0		// 默认类型为 Double
  ```

- 变量：var


#### 类型标注

```swift
let Length: Int

let a, b, c: Double
```

#### 命名

```swift
let π = 3.1415

let 🐶 = "Dog"

let 你好 = "你好世界"
```

#### 输出

```swift
let Length = 10

print(Length)
```

#### 字符串插值

```swift
print("The array' length is \(Length)")
```

### 注释

- 单行注释

  ```swift
  // 这是一行注释
  ```

- 多行注释

  ```swift
  /* 这是一个
  多行注释 */
  ```

- 嵌套注释

  ```swift
  /* 这是第一个多行注释的开头
   /* 这是第二个被嵌套的多行注释 */
  这是第一个多行注释的结尾 */
  ```


### 分号

- 不强制，但是在同一行有多条独立的语句就必须用

  ```swift
  let cat = "🐱"; print(cat)
  ```


### 整数

**有8，16，32和64位的有符号和无符号整数类型**

#### Int

- 在32位平台上，Int 和 Int32 长度相同
- 在64位平台上，Int 和 Int64 长度相同

#### UInt

- 在32位平台上，UInt 和 UInt32 长度相同
- 在64位平台上，UInt 和 UInt64 长度相同

### 浮点数

- Double表示64位浮点数，至少有15位数字
- Float表示32位浮点数，只有6位数字

### 类型安全和类型推断

**编译时就会进行类型检查，如果没有显式指定类型，Swift就会使用类型推断来选择合适的类型**

- ```swift
  let meaningOfLife = 42		// meaningOfLife 会被推测为 Int 类型
  ```


- 判断浮点数的时候，会优先选择**Double**型
  ```swift
  let pi = 3.14159		// pi 会被推测为 Double 类型
  ```
- 同时出现了整数和浮点数，会被推断为 Double 类型
  ```swift
  let anotherPi = 3 + 0.14159		// anotherPi 会被推测为 Double 类型
  ```

### 数值型字面量

**整数字面量可以被写作**

- 一个十进制数，**没有前缀**

- 一个二进制数，前缀是**0b**

- 一个八进制数，前缀是**0o**

- 一个十六进制数，前缀是**0x**

- 例：
  ```swift
  let decimalInteger = 17

  let binaryInteger = 0b10001		// 二进制的17

  let octalInteger = 0o21			// 八进制的17

  let hexadecimalInteger = 0x11	// 十六进制的17
  ```

### 数值型类型转换

#### 整数转换

类型不同不能直接相加，需进行类型的转换

```swift
let twoThousand: UInt16 = 2_000

let one: UInt8 = 1

let twoThousandAndOne = twoThousand + UInt16(one)
```

### 整数和浮点数转换

```swift
let three = 3

let pointOneFourOneFiveNine = 0.14159

let pi = Double(three) + pointOneFourOneFiveNine

let integerPi = Int(pi)		// 这种方式会将浮点数截断
```

### 类型别名——typealias

```swift
typealias AudioSample = UInt16

var maxAmplitudeFound = AudioSample.min		// maxAmplitudeFound = 0
```

### 元组

- **元组就是把多个值组合成一个复合值，元组内的值可以是任意类型**

  ```swift
  let http404Error = (404, "Not Found")

  let (statusCode, statusMessage) = http404Error

  print("The status code is \(statusCode)")		// 输出 The status code is 404

  print("The status message is \(statusMessage)")		// 输出 The status message is Not Found
  ```


- 若只需一部分元祖值，分解的时候把要忽略的部分用`_`代替即可：

  ```swift
  let (justTheStatusCode, _) = http404Error

  print("The status code is \(justTheStatusCode)")		// 输出 The status code is 404
  ```

- 通过下标访问

  ```swift
  print("The status code is \(http404Error.0)")		// 输出 The status code is 404"

  print("The status message is \(http404Error.1)")		// 输出 The status message is Not Found"
  ```

- 定义元祖时给元素命名

  ```swift
  let http200Status = (statusCode: 200, description: "OK")

  print("The status code is \(http200Status.statusCode)")		// 输出 The status code is 200

  print("The status message is \(http200Status.description)")		// 输出 The status message is OK
  ```

### 可选类型

#### 表示可能包含值也可能不包含值

- 例如：一个可选的 `Int` 被写作 `Int?` 而不是 `Int` ，问号暗示包含的值是可选类型，也就是说可能包含 `Int` 值也可能不包含值

  ```swift
  var serverResponseCode: Int? = 404		// serverResponseCode 包含一个可选的 Int 值 404
  ```

- 如果你声明一个可选常量或者变量但是没有赋值，它们会自动被设置为 nil：

  ```swift
  var surveyAnswer: String?		// surveyAnswer 被自动设置为 nil
  ```

#### 强制解析

- 使用感叹号`!`

  ```swift
  if convertedNumber != nil {
  	print("convertedNumber has an integer value of \(convertedNumber!).")
  }	// 输出 convertedNumber has an integer value of 123.
  ```

#### 可选绑定

- 判断可选类型是否包含值，如果包含就把值赋给一个临时常量或者变量

  ```swift
  if let constantName = someOptional {
      //statements
  }
  ```

#### 隐式解析可选类型

- 把想要用作可选的类型的后面的问号 `String?` 改成感叹号 `String!` 来声明一个隐式解析可选类型

  ```swift
  let possibleString: String? = "An optional string."

  let forcedString: String = possibleString!		// 需要感叹号来获取值
   
  let assumedString: String! = "An implicitly unwrapped optional string."

  let implicitString: String = assumedString		// 不需要感叹号
  ```

### 错误处理

- 使用 `throws` 抛出错误

  ```swift
  func canThrowAnError() throws {
      // 这个函数有可能抛出错误
  }
  ```

- 当函数能抛出错误消息时，前置需加上 `try` 关键字

  ```swift
  do {
      try canThrowAnError()
      // 没有错误消息抛出
  } catch {
      // 有一个错误消息抛出
  }
  ```

- 使用 `do` 语句创建了一个新的包含作用域,使得错误能被传播到一个或多个 `catch` 从句

  ```swift
  func makeASandwich() throws {
      // ...
  }
   
  do {
      try makeASandwich()
      eatASandwich()
      
  } catch SandwichError.outOfCleanDishes {
      washDishes()
      
  } catch SandwichError.missingIngredients(let ingredients) {
      buyGroceries(ingredients)
  }
  ```

### 断言

**结束代码运行并通过调试来找到值缺失的原因**

#### 使用断言进行调试

- 判断逻辑条件是否为 `true` ，若是，代码继续进行，否则，代码执行结束，并允许你附加一条调试信息

  ```swift
  let age = -3

  assert(age >= 0, "A person's age cannot be less than zero")		// 因为 age < 0，所以断言会触发

  assert(age >= 0)		// 如果不需要断言信息，可以省略
  ```



