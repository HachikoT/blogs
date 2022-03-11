- [x509证书扩展项（SubjectAlternativeName）](#x509证书扩展项subjectalternativename)
- [参考资料](#参考资料)

# x509证书扩展项（SubjectAlternativeName）

`SubjectAlternativeName`是x509 x3中的一个扩展项，该扩展项用于标记和界定证书持有者的身份。
对于证书持有者，一般使用`Subject`项标记，并使用`SubjectAlternativeName`扩展项提供更详细的持有者身份信息，缩写为`SAN`。它可以包括一个或者多个的电子邮件地址，域名，IP地址和URI等。

```bash
SubjectAltName ::= GeneralNames
GeneralNames ::= SEQUENCE SIZE (1..MAX) OF GeneralName

GeneralName ::= CHOICE {
    otherName                       [0]     OtherName,
    rfc822Name                      [1]     IA5String,
    dNSName                         [2]     IA5String,
    x400Address                     [3]     ORAddress,
    directoryName                   [4]     Name,
    ediPartyName                    [5]     EDIPartyName,
    uniformResourceIdentifier       [6]     IA5String,
    iPAddress                       [7]     OCTET STRING,
    registeredID                    [8]     OBJECT IDENTIFIER 
}
```

比如下面的一张x509证书。

```bash
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            5b:a1:65:99:6a:c6:77:24:2f:cd:1c:b2:fc:c9:bd:c6
        Signature Algorithm: ecdsa-with-SHA256
        Issuer: C = US, ST = California, L = San Francisco, O = 28ae74fc-be76-4af0-bcfc-735932bc2e6f, CN = tlsca.28ae74fc-be76-4af0-bcfc-735932bc2e6f
        Validity
            Not Before: Aug  3 08:43:00 2021 GMT
            Not After : Aug  1 08:43:00 2031 GMT
        Subject: C = US, ST = California, L = San Francisco, CN = bank-peer0.28ae74fc-be76-4af0-bcfc-735932bc2e6f
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (256 bit)
                pub:
                    04:77:6b:c4:7f:03:93:65:c8:d1:55:77:cc:54:b1:
                    7d:1e:98:da:f2:62:84:2e:ad:28:38:69:f3:10:a0:
                    25:2d:93:96:ed:bc:c7:9c:5c:97:e0:1f:13:97:87:
                    27:2b:76:c5:5e:fa:ee:2b:f4:64:97:26:4b:15:f2:
                    91:08:9e:2c:ce
                ASN1 OID: prime256v1
                NIST CURVE: P-256
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Authority Key Identifier: 
                keyid:83:3A:CD:79:DF:05:53:0F:EA:37:71:05:36:96:4E:C1:59:E2:6D:A7:02:E0:7B:83:22:51:5B:AD:7B:D5:18:8A

            X509v3 Subject Alternative Name: 
                DNS:bank-peer0.28ae74fc-be76-4af0-bcfc-735932bc2e6f, DNS:bank-peer0
    Signature Algorithm: ecdsa-with-SHA256
         30:45:02:21:00:a2:19:44:2f:25:3d:80:90:6f:d4:6c:98:bb:
         5c:26:f4:44:28:06:9c:4b:a6:bf:29:e4:d7:90:04:f5:8e:3f:
         75:02:20:13:33:49:23:c7:06:f0:52:65:d7:12:15:c4:3c:c4:
         46:c8:ab:ea:12:7d:66:2d:d1:bf:e0:1e:65:41:f5:2a:12
```

你去访问持有这个证书的服务器的时候，域名就只能写`bank-peer0.28ae74fc-be76-4af0-bcfc-735932bc2e6f`或者`bank-peer0`。不然就会`tls handshake failed`。

根据RFC-6125中的规定，当一个网站使用证书标记自己的身份时，如果证书中包含`SubjectAlternativeName`，在识别证书持有者时会忽略`Subject`子项，而是通过`SubjectAlternativeName`来识别证书持有者。

# 参考资料

- [证书签发与 SubjectAltName 扩展项](https://zhuanlan.zhihu.com/p/157911310)
