---
title: 基于token的鉴权机制
date: 2021-08-25 17:37:00
tags: Java
categories: 学习笔记
---

# 基于token的鉴权机制

流程：

1. 用户使用用户名密码来请求服务器
2. 服务器进行验证用户的信息
3. 服务器通过验证并发送给用户一个token
4. 客户端存储token，并在每次请求时在请求头附上这个token

5. 服务端验证token，并返回数据

这个token必须要在每次请求时传递给服务端，应该保存在请求头里，另外，服务端要支持CORS(跨来源资源共享)策略。

# JWT(Json Web Token)

JWT由三段信息构成，将这三段信息文本用`.`链接就构成了JWT字符串，如下：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

## JWT的构成

第一部分称为头部（header），第二部分称为载荷（payload），第三部分称为签证（signature）

### header

头部承载两部分信息：

- 声明类型

- 声明加密的算法，通常直接使用HMAC SHA256（第三部分加密使用）

完整头部就像如下JSON：

```json
{
  'typ': 'JWT',
  'alg': 'HS256'
}
```

然后将头部进行base64加密（该加密为对称加密），构成第一部分

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
```

### payload

载荷是存放有效信息的地方，包含三个部分

- 标准中注册的声明
- 公共声明
- 私有声明

标准声明（建议但不强制使用）

- iss：jwt签发者
- sub：jwt所面向的用户
- aud：接收jwt的一方
- exp：jwt的过期时间，这个过期时间必须大于签发时间
- nbf：定义在什么时间之前，该jwt都是不可用的
- iat：jwt的签发时间
- jti：jwt的唯一身份标识，主要用来作为一次性token，从而回避重放攻击

公共声明（Public claims）：

公共声明可以添加任何的信息，一般添加用户的相关信息或其他业务需要的必要信息，但不建议添加敏感信息，因为该部分在客户端可解密。

私有声明（**Private claims**）：

私有的声明是提供者和消费者所共同定义的声明，一般不建议存放敏感信息，因为base64是对称解密的。

定义一个payload：

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

然后对其进行base64加密，得到jwt的第二部分

```
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9
```

### signature

jwt的第三部分是一个签证信息，这个签证信息由三部分组成：

- header（base64加密后的）
- payload（base64加密后的）
- secret

将base64加密后的header和base64加密后的payload使用`.`连接组成字符串，然后通过header中声明的加密方式进行加盐`secret`组合加密，就构成了jwt的第三部分。

```javascript
var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload);

var signature = HMACSHA256(encodedString, 'secret'); // TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

将这三部分用`.`连接成一个完整的字符串就构成了最终的jwt：

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

注意：secret是保存在服务器端的，jwt的签发生成也是在服务器端的，secret就是用来进行jwt的签发和jwt的验证，所以，它就是你服务端的私钥，在任何场景都不应该流露出去。一旦客户端得知这个secret, 那就意味着客户端是可以自我签发jwt了。

## 应用

一般在请求头中加入`Authorization`，并加上`Bearer`标注：

```javascript
fetch('api/user/1', {
  headers: {
    'Authorization': 'Bearer ' + token
  }
})
```

流程如下图：

![鉴权流程](1821058-2e28fe6c997a60c9.png)

# 总结

- 因为json的通用性，所以JWT是可以进行跨语言支持的，像Java，JavaScript，Nodejs，Php等很多语言都可以使用。
- 因为有了payload部分，所以JWT可以在自身存储一些其他业务逻辑所必要的非敏感信息。
- 便于传输，jwt的构成非常简单，字节占用很小，所以非常便于传输。
- 它不需要在服务端保存会话信息，所以它易于应用的扩展。

# 安全

- 不应该在jwt的payload部分存放敏感信息，因为该部分是客户端可解密的部分。
- 需要保护好secret私钥，该私钥非常重要。
- 如果可以，使用https协议。

