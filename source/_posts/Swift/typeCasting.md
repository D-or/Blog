---
title: Swift 之旅 —— 类型转换
---

>   Swift 官方文档 类型转换 学习笔记

### 类型转换

#### 检查类型

```swift
class MediaItem {
    var name: String
    init(name: String) {
        self.name = name
    }
}

class Movie: MediaItem {
    var director: String
    init(name: String, director: String) {
        self.director = director
        super.init(name: name)
    }
}

class Song: MediaItem {
    var artist: String
    init(name: String, artist: String) {
        self.artist = artist
        super.init(name: name)
    }
}

let library = [
    Movie(name: "Casablanca", director: "Michael Curtiz"),
    Song(name: "Blue Suede Shoes", artist: "Elvis Presley")
]

var movieCount = 0
var others = 0

for item in library {
    if item is Movie {
        movieCount += 1
    } else {
        others += 1
    }
}

print(movieCount)       // 1
print(others)		// 1
```

#### 向下转型

```swift
for item in library {
    if let movie = item as? Movie {
        print("Movie: '\(movie.name)', dir: \(movie.director)")
    } else if let song = item as? Song {
        print("Song: '\(song.name)', by: \(song.artist)")
    }
}

// Movie: 'Casablanca', dir: Michael Curtiz
// Song: 'Blue Suede Shoes', by: Elvis Presley


```

#### Any 和 AnyObject 的类型转换

- **Any 可以表示任何类型，包括函数类型**
- **AnyObject 可以表示任何类类型的实例**

```swift
var things = [Any]()

things.append(0)
things.append(0.0)
things.append(42)
things.append(3.14159)
things.append("hello")
things.append((3.0, 5.0))
things.append(Movie(name: "Ghostbusters", director: "Ivan Reitman"))
things.append({ (name: String) -> String in "Hello, \(name)" })

for thing in things {
    switch thing {
    case 0 as Int:
        print("zero as an Int")
    case 0 as Double:
        print("zero as a Double")
    case let someInt as Int:
        print("an integer value of \(someInt)")
    case let someDouble as Double where someDouble > 0:
        print("a positive double value of \(someDouble)")
    case is Double:
        print("some other double value that I don't want to print")
    case let someString as String:
        print("a string value of \"\(someString)\"")
    case let (x, y) as (Double, Double):
        print("an (x, y) point at \(x), \(y)")
    case let movie as Movie:
        print("a movie called '\(movie.name)', dir. \(movie.director)")
    case let stringConverter as (String) -> String:
        print(stringConverter("Michael"))
    default:
        print("something else")
    }
}
// zero as an Int
// zero as a Double
// an integer value of 42
// a positive double value of 3.14159
// a string value of "hello"
// an (x, y) point at 3.0, 5.0
// a movie called 'Ghostbusters', dir. Ivan Reitman
// Hello, Michael

let optionalNumber: Int? = 3
things.append(optionalNumber)			// 警告
things.append(optionalNumber as Any) 	// 没有警告
```
