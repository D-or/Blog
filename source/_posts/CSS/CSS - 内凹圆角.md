---
title: CSS - 内凹圆角
---

>使用 CSS 实现内凹圆角

**使用 `border-radius` 就可以实现外凸圆角的效果：**

```css
.border {
    border-radius: 20px;
}
```

**那如果是内凹的效果呢？是不是这样就可以了：**

```css
.border {
    border-radius: -20px;
}
```

**但实际上它的语义不够准确，因此 CSS 工作组并没有接受这个提议，并未将它纳入标准，所以就得采取别的方式了，这时候就可以试试 `radial-gradient` 了：**

```react
.border {
  height: 10em;
  width: 10em;
  background: radial-gradient(circle at top left, transparent 40px, #58a 0) top left;
}

<div class="border"></div>
```

效果如下：

![748FA00E-1DF7-4116-8799-F7FA982B9489](/Users/H/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/3155965489/QQ/Temp.db/748FA00E-1DF7-4116-8799-F7FA982B9489.png)

