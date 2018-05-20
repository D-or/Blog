---
title: Swift 之旅 —— 高级运算符
---

>Swift 官方文档 高级运算符 学习笔记

### 高级运算符

#### 位运算符

- **按位取反`~`**

  ```swift
  let initialBits: UInt8 = 0b00001111
  let invertedBits = ~initialBits // 0b11110000
  ```

- **按位与 `&`**

- **按位或 `|`**

- **按位异或 `^`**

- **按位左移 `<<` ，右移 `>>`**

#### 溢出运算符

- **溢出加法 `&+`**

- **溢出减法 `&-`**

- **溢出乘法 `&*`**

- **实例**

  ```swift
  var unsignedOverflow = UInt8.max
  // unsignedOverflow 等于 UInt8 所能容纳的最大整数 255

  unsignedOverflow = unsignedOverflow &+ 1
  // 此时 unsignedOverflow 等于 0

  var unsignedOverflow = UInt8.min
  // unsignedOverflow 等于 UInt8 所能容纳的最小整数 0

  unsignedOverflow = unsignedOverflow &- 1
  // 此时 unsignedOverflow 等于 255

  var signedOverflow = Int8.min
  // signedOverflow 等于 Int8 所能容纳的最小整数 -128

  signedOverflow = signedOverflow &- 1
  // 此时 signedOverflow 等于 127
  ```

#### 运算符函数

```swift
struct Vector2D {
    var x = 0.0, y = 0.0
}

extension Vector2D {
    static func + (left: Vector2D, right: Vector2D) -> Vector2D {
        return Vector2D(x: left.x + right.x, y: left.y + right.y)
    }
}

let vector = Vector2D(x: 3.0, y: 1.0)

let anotherVector = Vector2D(x: 2.0, y: 4.0)

let combinedVector = vector + anotherVector
// combinedVector 是一个新的 Vector2D 实例，值为 (5.0, 5.0)
```

- **前/后缀运算符**

  ```swift
  extension Vector2D {
      static prefix func - (vector: Vector2D) -> Vector2D {
          return Vector2D(x: -vector.x, y: -vector.y)
      }
  }

  let positive = Vector2D(x: 3.0, y: 4.0)

  let negative = -positive
  // negative 是一个值为 (-3.0, -4.0) 的 Vector2D 实例

  let alsoPositive = -negative
  // alsoPositive 是一个值为 (3.0, 4.0) 的 Vector2D 实例
  ```

- **复合赋值运算符**

  ```swift
  extension Vector2D {
  	static func += (left: inout Vector2D, right: Vector2D) {
          left = left + right
      }
  }

  var original = Vector2D(x: 1.0, y: 2.0)

  let vectorToAdd = Vector2D(x: 3.0, y: 4.0)

  original += vectorToAdd
  // original 的值现在为 (4.0, 6.0)
  ```

- **等价运算符**

  ```swift
  extension Vector2D {
      static func == (left: Vector2D, right: Vector2D) -> Bool {
          return (left.x == right.x) && (left.y == right.y)
      }
      static func != (left: Vector2D, right: Vector2D) -> Bool {
          return !(left == right)
      }
  }

  let twoThree = Vector2D(x: 2.0, y: 3.0)

  let anotherTwoThree = Vector2D(x: 2.0, y: 3.0)

  if twoThree == anotherTwoThree {
      print("These two vectors are equivalent.")		// 输出 These two vectors are equivalent.
  }
  ```


#### 自定义运算符

**新的运算符要使用 operator 关键字在全局作用域内进行定义，同时还要指定 prefix、infix 或者 postfix 修饰符：**

```swift
prefix operator +++ {}
```

- **实例**

  ```swift
  extension Vector2D {
      static prefix func +++ (vector: inout Vector2D) -> Vector2D {
          vector += vector
          return vector
      }
  }

  var toBeDoubled = Vector2D(x: 1.0, y: 4.0)

  let afterDoubling = +++toBeDoubled
  // toBeDoubled 现在的值为 (2.0, 8.0)
  // afterDoubling 现在的值也为 (2.0, 8.0)
  ```

  ```swift
  infix operator +-: AdditionPrecedence

  extension Vector2D {
      static func +- (left: Vector2D, right: Vector2D) -> Vector2D {
          return Vector2D(x: left.x + right.x, y: left.y - right.y)
      }
  }

  let firstVector = Vector2D(x: 1.0, y: 2.0)

  let secondVector = Vector2D(x: 3.0, y: 4.0)

  let plusMinusVector = firstVector +- secondVector
  // plusMinusVector 是一个 Vector2D 实例，并且它的值为 (4.0, -2.0)
  ```
