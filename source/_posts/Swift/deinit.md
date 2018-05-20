---
title: Swift 之旅 —— 析构
---

>   Swift 官方文档 析构 学习笔记

### 析构

#### 析构器

```swift
deinit {
    // 执行析构过程
}
```

#### 实例

```swift
class Bank {
    static var coinsInBank = 10_000
    static func distribute(coins numberOfCoinsRequested: Int) -> Int {
        let numberOfCoinsToVend = min(numberOfCoinsRequested, coinsInBank)
        coinsInBank -= numberOfCoinsToVend
        return numberOfCoinsToVend
    }
    static func receive(coins: Int) {
        coinsInBank += coins
    }
}

class Player {
    var coinsInPurse: Int
    init(coins: Int) {
        coinsInPurse = Bank.distribute(coins: coins)
    }
    func win(coins: Int) {
        coinsInPurse += Bank.distribute(coins: coins)
    }
    deinit {
        Bank.receive(coins: coinsInPurse)
    }
}

var playerOne: Player? = Player(coins: 100)

print("A new player has joined the game with \(playerOne!.coinsInPurse) coins")     // 输出 A new player has joined the game with 100 coins

print("There are now \(Bank.coinsInBank) coins left in the bank")       // 输出 There are now 9900 coins left in the bank

playerOne = nil     // 调用析构函数，将实例 playerOne 中的 coinsInPurse 全部归还给银行

print(Bank.coinsInBank)     // 输出 10000
```