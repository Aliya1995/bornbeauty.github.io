---

layout: post
title: 应用安全方案(一)
category: 技术
tags: android
description:

---

# 1.安全问题要解决的四个问题

- 保密security/confidentiality:希望通信中的信息是加密的
- 鉴别/认证Authentication:鉴别与服务器通信的客户端就是我们自己的客户端
- 完整性integrity:若通信数据被中途篡改 能够检测出来
- 不可否认性non-repudiation:确定信息的来源

在互联网通信中，我们的数据往往其实就是公开的，没有绝对的物理隔绝，所以对数据加密就会先的尤为重要了。

# 2.典型的加密方式

加密方式主要可以分成对称加密和非对称加密方式。

## 2.1对称加密算法

所谓对称加密算法就是指在加密和解密过程中都使用一个相同的密钥。
流行的算法[DES](http://baike.baidu.com/item/DES)和[AES](https://zh.wikipedia.org/wiki/%E9%AB%98%E7%BA%A7%E5%8A%A0%E5%AF%86%E6%A0%87%E5%87%86)。

## 2.2非对称加密算法

与对称加密算法相反，加密和解密的密钥是不相同的。非对称加密对于对称加密的优势就在于在发布你的加密密钥的时候可以更加简单。我们不用考虑加密密钥被别人那走。这里，我门一般把加密密钥叫做公钥public-secret，解密密钥家叫做私钥private-secret。主要代表算法有[RSA机密算法](https://zh.wikipedia.org/wiki/RSA%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95)。

相比较对称加密，非对称加密是比较难以理解的。对称加密很好理解，我按照一定的算法使用key对数据加密，然后对应的，我想解密的时候就使用相同的key逆向执行一遍你运算就可以把数据还原回来了。但是非对称加密是怎么一个过程呢？其实它是基于大质数分解成两个质数之积是十分困难的。具体请见[加密算法](https://zh.wikipedia.org/wiki/RSA%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95#.E5.85.AC.E9.92.A5.E4.B8.8E.E7.A7.81.E9.92.A5.E7.9A.84.E4.BA.A7.E7.94.9F)。
