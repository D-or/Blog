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

### 身份验证

#### WWW-Authenticate

定义获取资源访问权应该使用的身份验证方法

#### Authorization

包含与服务器进行验证身份的凭证

#### Proxy-Authenticate

定义在代理服务器后获取资源访问权的身份验证方法

#### Proxy-Authorization

包含于代理服务器进行身份验证的凭证

### 缓存

#### Age

数据在代理缓存中的存在时间

####Cache-Control

指定请求或相应的缓存机制指令

####Expires

相应的过期时间

####Prama

特定实现的请求头，在请求相应链中的任何位置可能有各种各样的效果。向后兼容 HTTP/1.0 缓存，而那时 `Cache-Control` 还不存在

####Warning

一般情况下，包含可能出现的问题的信息

### Client hints

####Accept-CH

####Accept-CH-Lifetime

####Content-DPR

####DPR

####Downlink

#### Save-Data

#### Viewport-Width

#### Width

### Conditionals

#### Last-Modified

#### ETag

#### If-Match

#### If-None-Match





#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

#### Save-Data

v