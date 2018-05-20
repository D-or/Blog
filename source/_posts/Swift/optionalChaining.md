---
title: Swift 之旅 —— 可选链式
---

>   Swift 官方文档 可选链式 学习笔记

### 可选链式

#### 代替强制展开

```swift
class Person {
    var residence: Residence?
}

class Residence {
    var numberOfRooms = 1
}

let john = Person()		// john 的 residence 初始化为空

// let roomCount = john.residence!.numberOfRooms        // 报错

if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")        // 输出 Unable to retrieve the number of rooms.
}

john.residence = Residence()

if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")		// 输出 John's residence has 1 room(s).
}
```

#### 调用定义模型类

```swift
class Person {
    var residence: Residence?
}

class Residence {
    var rooms = [Room]()
    var numberOfRooms: Int {
        return rooms.count
    }
    subscript(i: Int) -> Room {
        get {
            return rooms[i]
        }
        set {
            rooms[i] = newValue
        }
    }
    func printNumberOfRooms() {
        print("The number of rooms is \(numberOfRooms)")
    }
    var address: Address?
}

class Room {
    let name: String
    init(name: String) { self.name = name }
}

class Address {
    var buildingName: String?
    var buildingNumber: String?
    var street: String?
    func buildingIdentifier() -> String? {
        if buildingName != nil {
            return buildingName
        } else if buildingNumber != nil && street != nil {
            return "\(buildingNumber) \(street)"
        } else {
            return nil
        }
    }
}
```

#### 调用访问属性

```swift
let john = Person()

if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// 输出 Unable to retrieve the number of rooms.

let someAddress = Address()
someAddress.buildingNumber = "29"
someAddress.street = "Acacia Road"
john.residence?.address = someAddress

func createAddress() -> Address {
    print("Function was called.")
    
    let someAddress = Address()
    someAddress.buildingNumber = "29"
    someAddress.street = "Acacia Road"
    
    return someAddress
}
john.residence?.address = createAddress()
```

#### 调用方法

```swift
if john.residence?.printNumberOfRooms() != nil {
    print("It was possible to print the number of rooms.")
} else {
    print("It was not possible to print the number of rooms.")
}
// 输出 It was not possible to print the number of rooms.

if (john.residence?.address = someAddress) != nil {
    print("It was possible to set the address.")
} else {
    print("It was not possible to set the address.")
}
// 输出 It was not possible to set the address.
```

#### 调用访问下标

```swift
if let firstRoomName = john.residence?[0].name {
    print("The first room name is \(firstRoomName).")
} else {
    print("Unable to retrieve the first room name.")
}
// 输出 Unable to retrieve the first room name.

john.residence?[0] = Room(name: "Bathroom")

let johnsHouse = Residence()
johnsHouse.rooms.append(Room(name: "Living Room"))
johnsHouse.rooms.append(Room(name: "Kitchen"))
john.residence = johnsHouse
 
if let firstRoomName = john.residence?[0].name {
    print("The first room name is \(firstRoomName).")
} else {
    print("Unable to retrieve the first room name.")
}
// 输出 The first room name is Living Room.
```

- **访问可选类型的下标**

  ```swift
  var testScores = ["Dave": [86, 82, 84], "Bev": [79, 94, 81]]
  testScores["Dave"]?[0] = 91
  testScores["Bev"]?[0] += 1
  testScores["Brian"]?[0] = 72
  // Dave 数组现在是 [91, 82, 84]，Bev 数组现在是 [80, 94, 81]
  ```

#### 连接多层调用

```swift
if let johnsStreet = john.residence?.address?.street {
    print("John's street name is \(johnsStreet).")
} else {
    print("Unable to retrieve the address.")
}
// 输出 Unable to retrieve the address.

let johnsAddress = Address()
johnsAddress.buildingName = "The Larches"
johnsAddress.street = "Laurel Street"
john.residence?.address = johnsAddress

if let johnsStreet = john.residence?.address?.street {
    print("John's street name is \(johnsStreet).")
} else {
    print("Unable to retrieve the address.")
}
// 输出 John's street name is Laurel Street.
```

#### 在方法的可选返回值上进行可选链式调用

```swift
if let buildingIdentifier = john.residence?.address?.buildingIdentifier() {
    print("John's building identifier is \(buildingIdentifier).")
}
// 输出 John's building identifier is The Larches.

if let beginsWithThe =
    john.residence?.address?.buildingIdentifier()?.hasPrefix("The") {
        if beginsWithThe {
            print("John's building identifier begins with \"The\".")
        } else {
        	print("John's building identifier does not begin with \"The\".")
        }
}
// 输出 John's building identifier begins with "The".
```
