---
title: HTTP和HTTPS
date: 2020-12-02 12:34:04
tags: Computer Networking, Interview
Hide: false
---
这是一份关于计算机网络的面试向准备笔记

Http是一个无状态协议，不记录客户端的相关信息
#### Http 和 Https 的区别
- Http：80端口； Https: 443 端口
- Http，超文本传输协议，明文传输。
  Https，运行在SSL上，添加了加密、认证机制，安全可靠。
- Https 需要更大的内存和CPU的开销
- Https 需要购买认证证书。

##### 加密算法
- 对称加密（速度快）
  加密和解密采用相同的密钥。 如： DES， RC2， RC4

- 非对称加密（更安全）
  由两部分：公钥、私钥。如果用公钥加密，需要使用私钥才能解密。如：RSA


- CA 认证证书
  第三方数字签名认证机构，为了确定https中服务器发送的公开密钥没有被篡改，因此会先发送给可信的第三方机构进行签名，客户端使用机构的公开密钥进行签名的认证，用来保证服务器公开密钥的可靠性。


- 数字签名、报文摘要的原理
  发送者用私钥进行签名，接收者用公钥进行验证。

#### Https的连接过程
![https](https://cdn.jsdelivr.net/gh/cw-Guo/cw-Guo.github.io/source/images/https.jpg)
- 客户端向服务端发送一个招呼报文（hello），包含自己支持的SSL版本，加密算法等信息。
- 服务端回复一个招呼报文（hi）包含自己支持的SSL版本，加密算法等信息。
- 服务端发送自己经过CA认证的公开密钥
  - 服务端向CA认证机构发送自己的公开密钥（FPkey）
  - CA认证机构使用自己的私有密钥给FPkey加上签名返回给服务端
- 服务端发送结束招呼的报文，SSL第一次握手结束。
- 客户端使用FPkey对自己的随机密码串（Ckey）进行加密并发送给服务端
  - 客户端首先使用CA的公开密钥对FPkey的签名进行认证，确认密钥未被替换
- 客户端发送提示报文，后续报文将用Ckey进行加密。
- 客户端发送finished报文，表示该次发送结束
  - 后续是否通信取决于客户端的finished报文能否被服务端成功解密
- 服务端发送提示报文，表示他之后的报文也会用Ckey进行加密
- 服务端发送finished报文。至此SSL握手结束，成功建立SSL连接。
- 客户端开始发送http请求报文
  - 建立Tcp连接，开始传输数据
- 服务端发送http回复报文
- 客户端发送断开连接报文，并断开Tcp连接

非对称加密算法用于在握手过程中加密生成的密码；对称加密算法用于对真正传输的数据进行加密；HASH算法用于验证数据的完整性。

#### 访问http如何自动转换为https
用户在地址栏输入地址，默认以http访问，此时服务器发送一个状态码：Status Code: 307 Internal Redirect
状态代码：307，交互重定向。其实也不一定是307，有些网站是302。
3xx状态码就是告诉客户端，请求的URI不存在，需要去访问另外一个URI。服务器把所有的HTTp流量跳转到HTTPS

一种更安全的机制：
HSTS，web安全协议，核心是一个HTTP响应头，强制拒绝用户的不安全链接，浏览器识别后自动访问https


#### HTTPS 中间人拦截过程
中间人拦截过程发生在第一个部分，拦截服务端返回的公钥，构建一对新的密钥伪装成公钥发送给客户端。然后拿到客户端的随机数，再用服务端的公钥进行加密，将密钥发送给服务端。
![https中间人拦截](https://cdn.jsdelivr.net/gh/cw-Guo/cw-Guo.github.io/source/images/https-attack.jpg)
  ------
  参考文章：
  [访问http如何自动转换为https](https://blog.csdn.net/weixin_37720172/article/details/105367086)

  https://zhuanlan.zhihu.com/p/79367346

  https://www.jianshu.com/p/8b9bb785eece