---
title: Beego 填坑记
---

1. **Q：CopyRequestBody 配置问题**

   **A：`app.conf` 中的 `copyrequestbody = true` 有时候会失效（在我的一个项目中它一直失效...），所以需要在 `main.go` 文件的 `main` 函数中添加如下一行代码**

   ```go
   beego.BConfig.CopyRequestBody = true
   ```

   

2. **Q：orm 使用前 package 的引入**

   **A：在 `main.go` 中需要引入数据库的驱动包，如：**

   ```go
   import (
       _ "github.com/go-sql-driver/mysql"
       ...
   )
   ```



3. **Q：Unknown column 'T0.i_d' in 'field list' []**

   **A：如下的结构，需要将 `ID` 改为 `Id`**

   ```go
   type Image struct {
   	ID    int    `orm:"pk;auto;index"`
       ...
   }
   ```

