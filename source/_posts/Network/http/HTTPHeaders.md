---
title: HTTP Headers
---

典型的头部属性使用 `X-` 作为前缀，但是这条规则在2012年就被弃用了，因为当一些不标准的的字段变为 RFC 6648 中的标准，以及其他一些先前被定义在 RFC 4229 中的字段被列在 IANA 注册表中时，这条规则造成了不便。IANA 也在维护一个提出新的 HTTP 信息头部的注册表。

HTTP 头部可以根据他们的上下文分为4种

- 一般头部

  适用于所有的请求和响应，但是与数据（尤其的主体中传输的数据）无关

- 请求头部

  包含更多关于将要被获取的资源或者客户端本身的信息

- 响应头部

  带有关于响应的额外的（比如他的位置）或者服务端本身（名字，版本等等）的信息

- 实体头部

  包含更多关于实体的主体部分（比如实体长度或者他的 MIME 类型）信息

也可以根据代理如何处理他们来分类

- End-to-End 头部

  这些头部必须传输到信息的最终接收者，就是请求的服务端或响应的客户端。中间代理必须继续传输未修改的 End-to-End 头部，并且缓存必须存储他们

- Hop-by-Hop 头部

  这些头部仅仅对单一的传输层连接有意义，并且不能被代理或缓存继续传输。这些头部有：`Connection` , `Keep-Alive` , `Proxy-Authenticate` , `Proxy-Authorization` , `TE` , `Trailer` , `Transfer-Encoding` 和 `Upgrade`。注意，只有 Hop-by-Hop 头部

---

### 身份验证

**WWW-Authenticate**

定义获取资源访问权应该使用的身份验证方法

**Authorization**

包含与服务器进行验证身份的凭证

**Proxy-Authenticate**

定义在代理服务器后获取资源访问权的身份验证方法

**Proxy-Authorization**

包含于代理服务器进行身份验证的凭证

---

### 缓存

**Age**

数据在代理缓存中的存在时间

**Cache-Control**

指定请求或相应的缓存机制指令

**Expires**

相应的过期时间

**Prama**

特定实现的请求头，在请求相应链中的任何位置可能有各种各样的效果。向后兼容 HTTP/1.0 缓存，而那时 `Cache-Control` 还不存在

**Warning**

一般情况下，包含可能出现的问题的信息

---

### 客户端提示

**Accept-CH**

**Accept-CH-Lifetime**

**Content-DPR**

**DPR**

**Downlink**

**Save-Data**

**Viewport-Width**

**Width**

---

### 条件

**Last-Modified**

验证器，资源的最后修改时间，用来比较同一资源的几个版本。没有 `ETag` 准确，但是在一些环境下更容易计算。使用 `If-Modified-Since` 和 `If-Unmodified-Since` 的请求使用这个字段来改变请求的行为

**ETag**

验证器，验证资源版本的独一无二字段。使用 `If-Match` 和 `If-None-Match` 的请求使用该字段来改变请求的行为

**If-Match**

使请求有条件，并且只在存储资源与给定的 `ETag` 之一匹配时才应用该方法

**If-None-Match**

使请求有条件，并仅在存储资源不与任何给定的 `ETag` 匹配时才应用该方法。这用于请求更新缓存（用于安全请求），或者在资源已经存在时避免防止上传新资源

**If-Modified-SInce**

使请求有条件，并期望只有在给定时间后才被修改的实体才会被传输。这只用于在缓存过期时传输数据

**If-Unmodified-Since**

使请求有条件，并期望实体仅在给定日期后未被修改的情况下才被传送。这用于确保特定范围的新片段与以前的片段的一致性，或者在修改现有文档时实现乐观的并发控制系统

---

### 连接管理

**Connection**

控制在当前事务完成后该网络连接是否保持打开状态

**Keep-Alive**

控制持久连接应该保持打开状态多长时间

---

### 内容协商

**Accept**

通知服务端可以发回的数据类型，即 MIME(Multipurpose Internet Mail Extensions)

**Accept-Charset**

通知服务端客户端可以识别哪个字符集

**Accept-Encoding**

通知服务端用于发回资源的编码算法，通常是一个压缩算法

**Accept-Language**

通知服务端期望发回的语言。这是一个提示，并且在用户的完全控制之下不一定需要：服务端应始终注意不要覆盖显示的用户选择（比如在一个下拉列表中选择一个语言）

---

### 控制

**Expect**

指示服务端需要满足的期望，以便正确地处理请求

**Max-Forwards**

---

###Cookies

**Cookie**

包含先前服务端发送的带有 `Set-Cookie` 头部存储的 HTTP cookies

**Set-Cookie**

从服务端发送 cookies 到用户代理

**Cookie2**

用于包含先前服务端发送的带有 `Set-Cookie` 头部的 HTTP cookie，但已被规范弃用。替换为 `Cookie` 

**Set-Cookie2**

用于将 cookies 从服务端发送到用户代理，但已被规范弃用。替换为 `Set-Cookie`

---

###CORS

**Access-Control-Allow-Origin**

表明所请求的资源是否接受来自指定 origin 的请求

**Access-Control-Allow-Credentials**

指示当 `Credential` 标志为 true 时，响应是否可以被得到

**Access-Control-Allow-Headers**

在响应预检请求时使用，以指示在发出实际请求时可以使用哪些请求头

**Access-Control-Allow-Methods**

指定访问资源时允许使用的方法

**Access-Control-Expose-Headers**

通过罗列他们的名字来指示响应请求头字段的白名单

**Access-Control-Max-Age**

指示请求的结果能缓存多长时间

**Access-Control-Request-Headers**

在发出预检请求时使用，以便在发出实际请求时让服务端知道将使用哪个 HTTP 头部

**Access-Control-Request-Method**

在发出预检请求时使用，以便在发出实际请求时让服务端知道将使用哪个 HTTP 方法

**Origin**

指示获取的来源

**Time-Allow-Origin**

指定允许通过 [Resource Timing API](https://developer.mozilla.org/en-US/docs/Web/API/Resource_Timing_API) 的特性查看属性值的原点，否则由于跨域限制，将其报告为零

---

### 参考

- [MDN - HTTP headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)
- [理解 CORS (Cross-Origin Resource Sharing)](http://huang-jerryc.com/2016/05/15/%E7%90%86%E8%A7%A3%20CORS%20(Cross-Origin%20Resource%20Sharing)/)