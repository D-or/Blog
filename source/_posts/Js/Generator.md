---
title: ES6 —— Generator
---

>   阮一峰《ECMAScript 6 入门》中的 Generator 学习笔记

### 介绍

-   **函数内部状态遍历器**
-   **每调用一次，函数内部状态就发生一次改变**

### 特征

-   **`function` 命令和函数名之间有一个星号 `*`**
-   **函数内部使用 `yield` 语句**

### 简单使用

```javascript
function* helloWorldGenerator() {
	console.log("Test");
  	yield "hello";
  	yield "world";
  	return "ending";
}

var hw = helloWorldGenerator();

hw.next()		// { value: 'hello', done: false }

hw.next()		// { value: 'world', done: false }

hw.next()		// { value: 'ending', done: true }

hw.next()		// { value: undefined, done: true }
```

### 理解

-   **第一次调用，函数执行，直到遇到第一个 `yield` 语句，程序暂停，再次调用时，就从上次暂停的地方继续执行，直到遇到下一个 `yield` 语句，以此类推...**
-   **注意：每次暂停都会返回一个对象 `{value: *, done: true/false}`，`value` 即为 `yield` 后面语句的值，`done` 的值为 true 或者false，表示遍历结束与否**

