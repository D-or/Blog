---
title: 在 Terminal 对文件的每一行数据 MD5 加密
---

```shell
cat filename | while read line; do echo $line | md5; done
```

