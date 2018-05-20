---
title: JS FAQ
---

> 学习 **JS** 过程中遇到的Question和Answer.

## 阐述下 use strict; 的作用

**use strict;** 顾名思义，就是 **JavaScript** 会在所谓**严格模式**下执行，也就是强制开发者避免使用未声明的变量，不过对于老版本的浏览器或者执行引擎则会自动忽略该指令

```javascript
// Example of strict mode

"use strict";

 
catchThemAll();

function catchThemAll() {

  x = 3.14;		// Error will be thrown

  return x * x;

}
```



## == 与 === 的区别是什么

**===** 也就是所谓的严格比较，关键的区别在于**===** 会同时比较**类型与值**，而不是仅比较值

```javascript
// Example of comparators

0 == false; // true

0 === false; // false


2 == '2'; // true

2 === '2'; // false
```



## null 与 undefined 的区别

- **null**

  是一个可以被分配的值，设置为 null 的变量意味着其无值

- **undefined**

  代表某个变量虽然声明了但是尚未进行过任何赋值

