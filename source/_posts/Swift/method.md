---
title: Swift 之旅 —— 方法
---

>   Swift 官方文档 方法 学习笔记

### 方法

#### 实例方法

```swift
class Counter {
    var count = 0
    func increment() {
        count += 1
    }
    /* 或者
    func increment() {
    	self.count += 1
    }
    */
    
    func increment(count count: Int) {
        self.count += count
    }
}

let counter = Counter()			// count 初始值为 0

counter.increment()				// count 为 1

counter.increment(count: 5)		// count 为 6
```

#### 类型方法

```swift
class SomeClass {
    class func someTypeMethod() {
        // 类型方法实现
    }
}
SomeClass.someTypeMethod()
```

