---
title: Swift 之旅 —— 控制流
---

>   Swift 官方文档 控制流 学习笔记

### 控制流

#### for-in 循环

```swift
var indexArr = [1, 2, 3, 4, 5]
for index in 1...5 {		//等同于 for index in indexArr
	print("\(index) times 5 is \(index * 5)")
}
// 1 times 5 is 5
// 2 times 5 is 10
// 3 times 5 is 15
// 4 times 5 is 20
// 5 times 5 is 25
```

```swift
let base = 3
let power = 10
var answer = 1
for _ in 1...power {
    answer *= base
}
print("\(base) to the power of \(power) is \(answer)")		// 输出 3 to the power of 10 is 59049
```

```swift
let numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
for (animalName, legCount) in numberOfLegs {
    print("\(animalName)s have \(legCount) legs")
}
// ants have 6 legs
// cats have 4 legs
// spiders have 8 legs
```

#### while 循环

```swift
var num = 5
while num < 8 {
    num += 1
}
print(num)		//输出 8
```

```swift
var num = 5
repeat {
    num += 1
} while num < 8
print(num)		//输出 8
```

#### 条件语句

- **if**

  ```swift
  var temperatureInFahrenheit = 90
  if temperatureInFahrenheit <= 32 {
      print("It's very cold. Consider wearing a scarf.")
  } else if temperatureInFahrenheit >= 86 {
  	print("It's really warm. Don't forget to wear sunscreen.")
  } else {		//可省略
      print("It's not that cold. Wear a t-shirt.")
  }
  // 输出 It's really warm. Don't forget to wear sunscreen
  ```

- **switch**

  ```swift
  //复合匹配
  let anotherCharacter: Character = "a"
  switch anotherCharacter {
  case "a", "A":
      print("The letter A")
  default:
      print("Not the letter A")
  }
  // 输出 The letter A
  ```

  ```swift
  //区间匹配
  let approximateCount = 6
  var naturalCount: String
  switch approximateCount {
  case 0:
      naturalCount = "no"
  case 1..<5:
      naturalCount = "a few"
  case 5..<10:
      naturalCount = "several"
  default:
      naturalCount = "many"
  }
  // naturalCount 现在为 several
  ```

  ```swift
  //元组
  let somePoint = (1, 1)
  switch somePoint {
  case (0, 0):
      print("(0, 0) is at the origin")
  case (_, 0):
      print("(\(somePoint.0), 0) is on the x-axis")
  case (0, _):
      print("(0, \(somePoint.1)) is on the y-axis")
  case (-2...2, -2...2):
      print("(\(somePoint.0), \(somePoint.1)) is inside the box")
  default:
      print("(\(somePoint.0), \(somePoint.1)) is outside of the box")
  }
  // 输出 "(1, 1) is inside the box"
  ```

#### 控制转移

- **continue**

- **break**

- **fallthrough**

  ```swift
  let integerToDescribe = 5
  var description = "The number \(integerToDescribe) is"
  switch integerToDescribe {
  case 2, 3, 5, 7, 11, 13, 17, 19:
      description += " a prime number, and also"
      fallthrough
  default:
      description += " an integer."
  }
  print(description)		// 输出 The number 5 is a prime number, and also an integer.
  ```

- **return**

  ```swift
  func greet(person: [String: String]) {
      guard let name = person["name"] else {
          return
      }
      print("Hello \(name)")
      guard let location = person["location"] else {
          print("I hope the weather is nice near you.")
          return
      }
      print("I hope the weather is nice in \(location).")
  }

  greet(person: ["name": "John"])
  // 输出 Hello John!
  // 输出 I hope the weather is nice near you.

  greet(person: ["name": "Jane", "location": "Cupertino"])
  // 输出 Hello Jane!
  // 输出 I hope the weather is nice in Cupertino.
  ```

- **throw**​
