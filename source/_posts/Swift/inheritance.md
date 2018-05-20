---
title: Swift 之旅 —— 继承
---

>   Swift 官方文档 继承 学习笔记

### 继承

#### 子类

```swift
class SubClass: Superclass {
    // 子类定义
}
```

#### 访问父类

```swift
//方法
super.SuperMethod()

//属性
super.SuperProperty

//下标
super[SuperIndex]
```

#### override

- **方法**

  ```swift
  class SubClass: Superclass {
  	override func SuperClass() {
  		// 重新定义
  	}
  }
  ```

- **属性**

  ```swift
  class SubClass: Superclass {
  	override var SuperProperty: Type {
  		// 重新定义
  	}
  }
  ```

- **属性观察器**

  ```swift
  class SubClass: Superclass {
      override var SuperProperty: Type {
          didSet {
          	// 重新定义
          }
      }
  }
  ```

#### 防止重写

```swift
final var

final func

final class func

final subscript
```

