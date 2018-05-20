---
title: Swift 之旅 —— 字符串和字符
---

>   Swift 官方文档 字符串和字符 学习笔记

### 字符串和字符

#### 初始化空字符

```swift
// 以下两个字符串均为空并等价
var emptyString = ""               // 空字符串字面量

var anotherEmptyString = String()  // 初始化方法

emptyString.isEmpty		//false，判断字符串是否为空
```

#### 字符串可变性

```swift
var variableString = "Horse"
variableString += " and carriage"		// variableString 现在为 Horse and carriage

var anotherString = " and carriage"
variableString.append(anotherString)		//结果与上面一样

//let constantString = "Highlander"
//constantString += " and another Highlander"		// 报错
```

#### 获取每个字符

```swift
for character in "Dog!🐶".characters {
	print(character)
}
// D
// o
// g
// !
// 🐶
```

#### 计算字符数量

```swift
string.characters.count
```

#### 访问字符串

- 索引

  ```swift
  let greeting = "Guten Tag!"

  greeting[greeting.startIndex]		// G

  greeting[greeting.index(before: greeting.endIndex)]		// !

  greeting[greeting.index(after: greeting.startIndex)]		// u

  let index = greeting.index(greeting.startIndex, offsetBy: 7)
  greeting[index]		// a

  for index in greeting.characters.indices {
     print("\(greeting[index]) ", terminator: "")
  }
  // 打印输出 G u t e n   T a g !
  ```

- 插入删除

  ```swift
  var welcome = "hello"
  welcome.insert("!", at: welcome.endIndex)
  // welcome 现在为 hello!
   
  welcome.insert(contentsOf:" there".characters, at: welcome.index(before: welcome.endIndex))
  // welcome 现在为 hello there!
  ```

  ```swift
  welcome.remove(at: welcome.index(before: welcome.endIndex))
  // welcome 现在等于 "hello there"
   
  let range = welcome.index(welcome.endIndex, offsetBy: -6)..<welcome.endIndex
  welcome.removeSubrange(range)
  // welcome 现在等于 hello
  ```

#### 前/后缀相同

```swift
let romeoAndJuliet = [
    "Act 1 Scene 1: Verona, A public place",
    "Act 1 Scene 2: Capulet's mansion",
    "Act 1 Scene 3: A room in Capulet's mansion"
]

var act1SceneCount = 0
for scene in romeoAndJuliet {
    if scene.hasPrefix("Act 1") {
        act1SceneCount += 1
    }
}
print("There are \(act1SceneCount) scenes in Act 1")		// 输出 There are 3 scenes in Act 1

var mansionCount = 0
for scene in romeoAndJuliet {
    if scene.hasSuffix("Capulet's mansion") {
        mansionCount += 1
    }
}
print("\(mansionCount) mansion scenes")		// 输出 2 mansion scenes
```