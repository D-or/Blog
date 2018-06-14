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

  这些头部仅仅对单一的传输层连接有意义，并且不能被代理或缓存继续传输。这些头部有：`Connection` , `Keep-Alive` , `Proxy-Authenticate` , `Proxy-Authorization` , `TE` , `Trailer` , `Transfer-Encoding` 和 `Upgrade`

---

### HTTP/1.1 首部字段

**通用首部**

|    首部字段名     |            说明            |
| :---------------: | :------------------------: |
|   Cache-Control   |       控制缓存的行为       |
|    Connection     |    逐跳首部、连接的管理    |
|       Date        |     创建报文的日期时间     |
|      Pragma       |          报文指令          |
|      Trailer      |     报文末端的首部一览     |
| Transfer-Encoding | 指定报文主体的传输编码方式 |
|      Upgrade      |       升级为其他协议       |
|        Via        |    代理服务器的相关信息    |
|      Warning      |          错误通知          |

**请求首部**

|     首部字段名      |                      说明                       |
| :-----------------: | :---------------------------------------------: |
|       Accept        |            用户代理可处理的媒体类型             |
|   Accept-Charset    |                  优先的字符集                   |
|   Accept-Encoding   |                 优先的内容编码                  |
|   Accept-Language   |             优先的语言（自然语言）              |
|    Authorization    |                  Web 认证信息                   |
|       Expect        |              期待服务器的特定行为               |
|        From         |               用户的电子邮箱地址                |
|        Host         |               请求资源所在服务器                |
|      If-Match       |              比较实体标记（ETag）               |
|  If-Modified-Since  |               比较资源的更新时间                |
|    If-None-Match    |         比较实体标记（与If-Match相反）          |
|      If-Range       |      资源未更新时发送实体 Byte 的范围请求       |
| If-Unmodified-Since | 比较资源的更新时间（与 If-Modified-Since 相反） |
|    Max-Forwards     |                 最大传输逐调数                  |
| Proxy-Authorization |         代理服务器要求客户端的认证信息          |
|        Range        |               实体的字节范围请求                |
|       Referer       |            对请求中 URI 的原始获取方            |
|         TE          |                传输编码的优先级                 |
|     User-Agent      |              HTTP 客户端程序的信息              |

**响应首部**

|     首部字段名     |             说明             |
| :----------------: | :--------------------------: |
|   Accept-Ranges    |     是否接收字节范围请求     |
|        Age         |     推算资源创建经过时间     |
|        ETag        |        资源的匹配信息        |
|      Location      |   令客户端重定向至指定 URI   |
| Proxy-Authenticate | 代理服务器对客户端的认证信息 |
|    Retry-After     |   对再次发起请求的时机要求   |
|       Server       |    HTTP 服务器的安装信息     |
|        Vary        |   代理服务器缓存的管理信息   |
|  WWW-Authenticate  |   服务器对客户端的认证信息   |

**实体首部**

|    首部字段名    |             说明             |
| :--------------: | :--------------------------: |
|      Allow       |    资源可支持的 HTTP 方法    |
| Content-Encoding |    实体主体适用的编码方式    |
| Content-Language |      实体主体的自然语言      |
|  Content-Length  | 实体主体的大小（单位：字节） |
| Content-Location |      替代对应资源的 URI      |
|   Content-MD5    |      实体主体的报文摘要      |
|  Content-Range   |      实体主体的位置范围      |
|   Content-Type   |      实体主体的媒体类型      |
|     Expires      |    实体主体过期的日期时间    |
|  Last-Modified   |    资源的最后修改日期时间    |

**非HTTP/1.1 首部字段**

**`Cookie` , `Set-Cookie` , `Content-Disposition`**

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

```h
Cache-Control: private, max-age=0, no-cache
```

- 请求指令

  |      |      |
  | :--: | :--: |
  |      |      |

  

**Expires**

相应的过期时间

**Prama**

特定实现的请求头，在请求相应链中的任何位置可能有各种各样的效果。向后兼容 HTTP/1.0 缓存，而那时 `Cache-Control` 还不存在

```
Pragma: no-cache
```

如果所有的中间服务器都以 HTTP/1.1 为基准，则直接采用 `Cache-Control: no-cache`，否则一般都会同时使用下面两个首部字段

```
Cache-Control: no-cache
Pragma: no-cache
```

**Warning**

一般情况下，包含可能出现的与缓存相关的问题的信息

```
// 格式
Warning: [警告码][警告的主机:端口号]"[警告的内容]"([日期时间<可选>])

// 举例
Warning: 113 example.com:8080 "Heuristic expiration" Tue, 28 Feb => 2018 00:00:00 GMT
```

HTTP/1.1 定义了7种警告

| 警告码 |                     警告内容                     |                             说明                             |
| :----: | :----------------------------------------------: | :----------------------------------------------------------: |
|  110   |         Response is stale（响应已过期）          |                     代理返回已过期的资源                     |
|  111   |        Revalidation failed（再验证失败）         |       代理再验证资源有效性失败（服务器无法到达等原因）       |
|  112   |     Disconnection operation（断开连接操作）      |                  代理与互联网连接被故意切断                  |
|  113   |        Heuristic expiration（试探性过期）        | 响应的使用期超过24小时（有效缓存的设定时间大于24小时的情况下） |
|  199   |        Miscellaneous warning（杂项警告）         |                        任意的警告内容                        |
|  214   |       Transformation applied（使用了转换）       |          代理对内容编码或媒体类型等执行了某些处理时          |
|  299   | Miscellaneous persistent warning（持久杂项警告） |                        任意的警告内容                        |

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

- 控制不再转发给代理的首部字段（即 hop-by-hop 首部）

  ```
     (客户端)
  GET / HTTP/1.1                                                   (服务端)
  Upgrade: HTTP/1.1      --->         代理         --->         GET / HTTP/1.1
  Connection: Upgrade
  ```

- 管理持久连接

  - HTTP/1.1 的默认连接是持久连接

    ```
                               GET / HTTP/1.1
                               Connection: Keep-Alive
                     ------------------------------------------>
    客户端                                                                 服务端
                     <------------------------------------------
                               HTTP/1.1 200 OK
                               ...
                               Keep-Alive: timeout=10, max=500
                               Connection: Keep-Alive
    ```

  - 服务器明确想断开

    ```
    Connection: close
    ```

HTTP/1.1 之前的版本的默认连接都是非持久连接

**Keep-Alive**

控制持久连接应该保持打开状态多长时间

---

### 内容协商

**Accept**

告知服务端用户代理能够处理的媒体类型及相对优先级，即 MIME(Multipurpose Internet Mail Extensions)

```
Accept: text/html, application/xhtml+xml, application/xml;q=0.9, */*;q=0.8
```

媒体类型

- 文本文件

  ```
  text/html, text/plain, text/css ...
  application/xhtml+xml, application/xml ...
  ```

- 图片文件

  ```
  image/jpeg, image/gif, image/png ...
  ```

- 视频文件

  ```
  video/mpeg, video/quicktime ...
  ```

- 应用程序使用的二进制文件

  ```
  application/octet-stream, application/zip ...
  ```

使用 **`q=[权重(0~1)]`** 表示优先级，不指定则默认为 `q=1.0`；当服务端提供多种内容时，首先返回权重值最高的内体类型；也可使用 **`*`** 作为通配符，指定任何类型

**Accept-Charset**

告知服务端用户代理支持的字符集以及相对优先顺序，同样使用 **`q`** 表示优先级，也可使用 **`*`**

```
Accept-Charset: iso-8859-5, unicode-1-1;q=0.8
```

**Accept-Encoding**

告知服务端用户代理支持的编码及优先级，同样使用 **`q`** 表示优先级，也可使用 **`*`**

```
Accept-Encoding: gzip, deflate
```

编码类型

- gzip

  由文件压缩程序 gzip （GNU zip）生成的编码格式，采用 Lempel-Ziv 算法（LZ77）及 32 位循环冗余校验（CRC）

- compress

  由 UNIX 文件压缩程序 compress 生成的编码格式，采用 Lempel-Ziv-Welch 算法（LZW）

- deflate

  组合使用 zlib 格式及由 deflate 压缩算法生成的编码格式

- identity

  不执行压缩或不会变化的默认编码格式

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

### Do Not Track

**DNT**

用于表示用户的跟踪偏好

**TK**

指示应用于相应请求的跟踪状态

---

### 下载

**Content-Disposition**

响应头部，如果传输的资源应以内联方式显示（标题不存在时的默认行为），或者应该像下载一样处理，浏览器应显示“另存为”窗口

---

### 消息主体信息

**Content-Length**

指示发送给接受者的实体主体大小

**Content-Type**

指示资源类型

**Content-Encoding**

用于指定压缩算法

**Content-Language**

指示主体使用的语言

**Content-Location**

指示返回数据的备用地址

---

### Proxies

**Forwarded**

包含来自代理服务器面向客户端的信息，当代理涉及到请求路径时，这些信息将被修改或丢失

**X-Forwarded-For**

标识通过 HTTP 代理或负载均衡器连接到 web 服务器的客户端的原始 IP 地址

**X-Forwarded-Host**

标识用于连接到代理或负载均衡器的客户端请求的原始主机

**X-Forwarded-Proto**

标识客户端连接到代理或负载均衡器的协议（HTTP 或 HTTPS）

**Via**

由代理（正向和反向代理）添加，追踪客户端与服务器之间的请求和响应报文的传输路径。与 traceroute 及电子邮件的 Received 首部的工作机制类似

```
                                                                   GET / HTTP/1.1
                                    GET / HTTP/1.1                 Via: 1.0 example.com，
       GET / HTTP/1.1               Via: 1.0 example.com                1.1 test.com
客户端 ----------------> 代理服务器A ------------------> 代理服务器B ----------------> 源服务器
```



---

### 参考

- [MDN - HTTP headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)
- [理解 CORS (Cross-Origin Resource Sharing)](http://huang-jerryc.com/2016/05/15/%E7%90%86%E8%A7%A3%20CORS%20(Cross-Origin%20Resource%20Sharing)/)