---
title: Swift 之旅 —— 类和结构体
---

>   Swift 官方文档 类和结构体 学习笔记

### 类和结构体

#### 定义

```swift
struct Resolution {
    var width = 0
    var height = 0
}

class VideoMode {
    var resolution = Resolution()
    var interlaced = false
    var frameRate = 0.0
    var name: String?
}
```

#### 实例

```swift
let someResolution = Resolution()

let someVideoMode = VideoMode()
```

#### 属性访问

```swift
print("The width of someResolution is \(someResolution.width)")		// 输出 The width of someResolution is 0

print("The width of someVideoMode is \(someVideoMode.resolution.width)")		// 输出 The width of someVideoMode is 0

someVideoMode.resolution.width = 1280
print("The width of someVideoMode is now \(someVideoMode.resolution.width)")		// 输出 The width of someVideoMode is now 1280

//结构体成员逐一构造器
let vga = Resolution(width:640, height: 480)
```

#### 结构体和枚举是值类型

```swift
let hd = Resolution(width: 1920, height: 1080)
var cinema = hd
cinema.width = 2048
print("cinema is now  \(cinema.width) pixels wide")		// 输出 cinema is now 2048 pixels wide

print("hd is still \(hd.width) pixels wide")		// 输出 hd is still 1920 pixels wide

enum CompassPoint {
    case North, South, East, West
}
var currentDirection = CompassPoint.West
let rememberedDirection = currentDirection
currentDirection = .East
if rememberedDirection == .West {
    print("The remembered direction is still .West")		// 输出 The remembered direction is still .West
}
```

#### 类是引用类型

```swift
struct Resolution {
    var width = 0
    var height = 0
}

class VideoMode {
    var resolution = Resolution()
    var interlaced = false
    var frameRate = 0.0
    var name: String?
}

let hd = Resolution(width: 1920, height: 1080)

let tenEighty = VideoMode()
tenEighty.resolution = hd
tenEighty.interlaced = true
tenEighty.name = "1080i"
tenEighty.frameRate = 25.0

let alsoTenEighty = tenEighty
alsoTenEighty.frameRate = 30.0

print("The frameRate property of tenEighty is now \(tenEighty.frameRate)")		// 输出 The frameRate property of theEighty is now 30.0
```
