# UUID

UUID(Universally Unique IDentifier)是一个128位bit的唯一标识。
UUID使用16进制表示，共有36个字符(32个16进制字符+4个连接符"-")，格式为8-4-4-4-12，如：

```text
6d25a684-9558-11e9-aa94-efccd7a0659b
xxxxxxxx-xxxx-Mxxx-Nxxx-xxxxxxxxxxxx
```

M中使用4位bit来表示UUID的版本，N中使用1-3位bit表示不同的变体（variant）。如上面所示：M=1，N=a表示此UUID为version-1，variant-1的UUID(Time-based ECE/RFC 4122 UUID)。

# UUID版本

UUID现有5种版本，是根据不同的使用场景划分的，而不是根据精度，所以version5并不会比version1精度高，在精度上，大家都能保证唯一性，重复的概率近乎于0。

## version1(date-time MAC-address)

基于时间戳及MAC地址的UUID实现。它包括了48位的MAC地址和60位的时间戳。
由于在算法中使用了MAC地址，这个版本的UUID可以保证在全球范围的唯一性。但与此同时，因为它暴露了电脑的MAC地址和生成这个UUID的时间，这就是这个版本UUID被诟病的地方，使用MAC地址会带来安全性问题。如果应用只是在局域网中使用，也可以使用退化的算法，以IP地址来代替MAC地址。
v1为了保证唯一性，当时间精度不够时，会使用13~14位的clock sequence来扩展时间戳，就是当系统时间精度无会区分2个UUID的时间先后时，为了保证唯一性，会在其中一个UUID上+1。

## version2(date-time Mac-address)

DCE（Distributed Computing Environment）安全的UUID。和基于时间的UUID算法相同，但会把时间戳的前4位置换为POSIX的UID或GID。
这是最神秘的版本，RFC没有提供具体的实现细节，以至于大部分的UUID库都没有实现它。

## version3,5(namespace-name based)

v3和v5都是通过hash namespace的标识符和名称生成的。v3使用MD5作为hash函数，v5则使用SHA-1。
因为里面没有不确定的部分，所以当namespace与输入参数确定时，得到的UUID都是确定唯一的。

## version4(random)

这个版本使用最为广泛。其中4位代表版本，2-3位代表variant。余下的122-121位都是全部随机的。

# 参考资料

- 知乎文章：[UUID如何保证唯一性？](https://zhuanlan.zhihu.com/p/70375430)
- CSDN文章：[UUID详解](https://blog.csdn.net/zzhongcy/article/details/106354946)
