---
title: 正则匹配
---

### 汉字

```go
reg := "^\\p{Han}+$"
m, _ := regexp.MatchString(reg, "你好")
fmt.Println(m)    // m: true
```

