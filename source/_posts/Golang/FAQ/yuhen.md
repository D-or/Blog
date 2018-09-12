---
title: 学习笔记 - Go 语言学习笔记
---

> 雨痕 - Go语言学习笔记

#### 输出完整的调用堆栈信息

```go
package main

import (
	"runtime/debug"
)

func test() {
	panic("Dead")
}

func main() {
	defer func() {
		if err := recover(); err != nil {
			debug.PrintStack()
		}
	}()

	test()
}
```

#### 字符串

**默认值**

是 `""` ，不是 `nil`

**`**

定义不做转义处理的原始字符串（raw string），支持跨行

```go
func main() {
	s := `line\r\n,
	line 2`

	println(s)
}

// 输出:
// line\r\n,
// line 2
```

**for 遍历字符串**

```go
func main() {
	s := "雨痕"

	for i := 0; i < len(s); i++ { //byte
		fmt.Printf("%d: [%c]\n", i, s[i])
	}
    
    fmt.Println()

	for i, c := range s {         //rune: 返回数组索引号，以及Unicode字符
		fmt.Printf("%d: [%c]\n", i, c)
	}
}

// 输出:
// 0: [é]
// 1: [
// 2: [¨]
// 3: [ç]
// 4: []
// 5: []
//
// 0: [雨]
// 3: [痕]
```

**appen([]byte, string)**

```go
func main() {
	var bs []byte
	bs = append(bs, "abc"...)

	fmt.Println(bs)
}

// 输出:
// [97 98 99]
```

**rune, byte, string**

使用 *单引号* 的变量，默认类型为 `rune`（int32 的别名）

- 相互转换

  ```go
  r1 := '我'
  
  s1 := string(r1)    // rune to string
  b1 := byte(r1)      // rune to byte
  
  s2 := string(b1)    // byte to string
  r2 := rune(b1)      // byte to rune
  ```

#### 数组

**初始化**

```go
var a [4]int               // 元素自动初始化为零

b := [4]int{2, 5}          // 未提供初始值的元素自动初始化为0
c := [6]int{5, 3: 10}      // 可指定索引位置初始化

d := [...]int{1, 2, 3}     // 编译器按初始化值数量确定数组长度
e := [...]int{10, 2: 100}  // 支持索引初始化，但注意数组长度与此有关

// a: [0 0 0 0]
// b: [2 5 0 0]
// c: [5 0 0 10 0 0]
// d: [1 2 3]
// e: [10 0 100]
```

**创建**

```go
var a []int     // 仅定义了一个[]int类型变量，并未执行初始化操作
b := []int{}    // 用初始化表达式完成了全部创建过程
println(a == nil, b == nil)

// true false
```

#### 字典

字典对象本身就是指针包装，传参时无须再次取地址

**len, cap**

`len` 返回当前键值对数量，`cap` 不接受字典类型

**修改**

因内存访问安全和哈希算法等缘故，字典被设计成 ”not addressable“，故不能直接修改value成员（结构或数组）

```go
func main() {
	type user struct {
		name string
		age  byte
	}

	m := map[int]user{
		1: {"Tom", 19},
	}

	m[1].age += 1    // 错误: cannot assign to struct field m[1].age in map
    // 正确做法
    // u := m[1]
	// u.age++
	// m[1] = u
}
```

**初始化**

对 `nil` 字典，只能读不能写

```go
func main() {
	var m map[string]int
    println(m["a"])                // 返回零值: 0
	m["a"] = 1                     // panic: assignment to entry in nil map
}

func main() {
	var m1 map[string]int
	m2 := map[string]int{}         // 已初始化，等同 make 操作

	println(m1 == nil, m2 == nil)  // true false
}
```

#### 结构

**空结构(struct{})**

没有字段的结构类型，比较特殊，因为无论是其自身，还是作为数组元素类型，其长度都为零

尽管没有分配数组内存，但依然可以操作元素，对应切片len、cap属性也正常

```go
func main() {
	var a struct{}
	var b [100]struct{}

	println(unsafe.Sizeof(a), unsafe.Sizeof(b))
    
    var d [100]struct{}
	s := d[:]

	d[1] = struct{}{}
	s[2] = struct{}{}

    fmt.Println(s[3], len(s), cap(s))
}

// 输出:
// 0 0
// {} 100 100
```

而且，这类“长度”为零的对象通常都指向runtime.zerobase变量

```go
func main() {
	a := [10]struct{}{}
	b := a[:]    // 底层数组指向zerobase，而非slice
	c := [0]int{}

	fmt.Printf("%p, %p, %p\n", &a[0], &b[0], &c)
}

// 输出:
// 0x1157c08, 0x1157c08, 0x1157c08
```

#### 方法

如果方法内部不引用实例，可省略参数名，保留类型

```go
type N int

func (N) Hello() {
    println("Hello!")
}
```

如何选择方法的实例类型？

- 要修改实例状态，用 ***T**
- 无须修改状态的小对象或固定值，建议用 **T**
- 大对象建议用 ***T**，以减少复制成本
- 引用类型、字符串、函数等指针包装对象，直接用 **T**
- 若包含 `Mutex` 等同步字段，用 ***T**，避免因复制造成锁操作无效
- 其他无法确定的情况，都用 ***T**

















