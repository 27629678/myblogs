## 签名mobileconfig文件

### 一、mobileconfig文件

文件的生成不再多表，可以通过`Apple Configurator 2`生成，或者编程拼接配置文件都可以；

### 二、签名方式

#### 2.1 Mac OSX平台

利用`KeyChain`和`security`命令行工具对文件进行签名

```
$security cms -S -N "Mac Developer Application" -i /path/to/your.mobileconfig -o /path/to/your/signed/output.mobileconfig
```

参考链接[请点击这里](https://osxdominion.wordpress.com/2015/04/21/signing-mobileconfig-profiles-with-keychain-certificates/)

#### 2.2 openssl签名方式

```
$openssl smime -sing -signer cer.pem -inkey pri.key -certfile intermediate.pem -outform der -nodetach -in unsigned.mobileconfig -out signed.mobileconfig
```

参数分析：

- signer: 签名所使用的证书
- inkey: 证书的私钥
- certfile: 颁发证书时使用的证书

参考链接[请点击这里](https://9elements.com/io/signing-configuration-profiles-with-openssl-in-pure-ruby/)