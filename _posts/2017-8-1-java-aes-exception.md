---
layout: post  
title: java中AES加密算法异常  
category: program  
tags: [mysql]  

--- 

##问题描述
在学习AES算法时，从互联网上找了例子，但是在自己运行下一直报错，错误信息如下：

```java

javax.crypto.BadPaddingException: Given final block not properly padded
	at com.sun.crypto.provider.CipherCore.doFinal(CipherCore.java:811)
	at com.sun.crypto.provider.CipherCore.doFinal(CipherCore.java:676)
	at com.sun.crypto.provider.AESCipher.engineDoFinal(AESCipher.java:313)
	at javax.crypto.Cipher.doFinal(Cipher.java:2087)

```

经过google，发现很多人出现类似的问题，有很多人说是不同操作系统造成的，windows是正常的，走到linux下面就不正常了。我尝试了一下，同样的代码在mac和windows都会出现这个错误，可见这个问题不一定是操作系统造成的。但是
在java代码中如果你在加密和解密的过程中使用的同一个secretKey对象，就不会报错。

原因一：SecureRandom 实现完全随操作系统本身的内部状态，除非调用方在调用 getInstance 方法之后又调用了 setSeed 方法；该实现在 windows 上每次生成的 key 都相同，但是在 solaris 或部分 linux 系统上则不同。

原因二：加密和解密时使用的密钥不一致。所以怀疑是由于生成的密钥不同造成的。`SecureRandom random = new SecureRandom();`这个对象每次调用生成的密钥都不一样，所以在加密和解密过程如果这个密钥不一致就会出错。当然我们也可以指定生成密钥的种子，让密钥生成每次都一样`random.setSeed("123456".getBytes());`即可。

##解决方式

```java

KeyGenerator kgen = KeyGenerator.getInstance("AES");
			SecureRandom secureRandom = SecureRandom.getInstance("SHA1PRNG");
			secureRandom.setSeed(key.getBytes());
			kgen.init(128, secureRandom);

```