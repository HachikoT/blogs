- [RSA](#rsa)
- [RSA公私钥生成步骤](#rsa公私钥生成步骤)
- [RSA加解密步骤](#rsa加解密步骤)
- [RSA安全性](#rsa安全性)
- [参考资料](#参考资料)

# RSA

1977年，三位数学家Rivest、Shamir和Adleman设计了一种算法，可以实现非对称加密。算法用他们三个人的名字命名，叫做RSA算法。
RSA算法的安全性是基于判断一个数是否为质数很简单，但是要对一个数进行质因子分解很困难这样一个事实。

# RSA公私钥生成步骤

1. 找两个大的质数$p$和$q$，越大越不容易破解，计算$p$和$q$的乘积$n$

$$n=p\times q$$

2. 计算$n$的欧拉函数$\varphi(n)$，由[欧拉函数公式](https://www.cnblogs.com/HachikoT/p/13910985.html)可得。

$$\varphi(n)=(p-1)(q-1)$$

3. 找一个和$\varphi(n)$互质的整数$e$，其中$1 < e < \varphi(n)$，在openssl中$e$固定为65537。

$$gcd(e,\varphi(n))=1$$

4. 计算整数$e$模$\varphi(n)$的乘法逆元$d$

$$ed\equiv 1\,mod\,\varphi(n)$$

最终，将$(n,d)$作为私钥，$(n,e)$作为公钥

# RSA加解密步骤

下面是用私钥加密，公钥解密的过程：

- **加密**：

$$X^d\,mod\,n=Y$$

- **解密**：

$$Y^e\,mod\,n=X$$

这里可以还原出加密的数据是因为

$$
\begin{aligned}
(X^d)^e &\equiv X^{de}\,mod\,n\\
&\equiv X^{k\varphi(n)}X\,mod\,n\\
&\equiv X\,mod\,n\\
\end{aligned}
$$

上面的推导中利用了[欧拉定理](https://www.cnblogs.com/HachikoT/p/13922622.html)：$X^{\varphi(n)}\equiv 1\,mod\,n$，但这需要$X$与$n$互质。
当$X$不与$n$互质时，只可能$X=tp$或者$X=tq$，这里推导$X=tp$的情况，这里$tp$与$q$一定是互质的。此时由[欧拉函数公式](https://www.cnblogs.com/HachikoT/p/13910985.html)可得。

$$
\begin{aligned}
(tp)^{\varphi(q)} &\equiv 1\,mod\,q\\
(X)^{\varphi(q)} &\equiv 1\,mod\,q\\
(X)^{k\varphi(q)\varphi(p)} &\equiv 1\,mod\,q\\
(X)^{\varphi(n)}X &\equiv X\,mod\,q\\
(X)^{de} &\equiv X\,mod\,q\\
\end{aligned}
$$

推出

$$(tp)^{de}=t'q+tp$$

由于$p|t'q$所以

$$(tp)^{de}=t''pq+tp$$

上式等价于

$$(tp)^{de}\equiv tp\,mod\,n$$
$$(X)^{de}\equiv X\,mod\,n$$

这样也就证明了，即使$X$和$n$不互质也是可以正确加密和解密的。

# RSA安全性

RSA公私钥生成过程中可知，要从$e$推导出$d$或者相反，必须要知道数$n$的欧拉函数$\varphi(n)=(p-1)(q-1)$
所以RSA算法的安全性就等价于对大整数$n$进行因式分解找出大质数$p$和$q$，而目前质因子分解并没有多项式解法，所以可能保证一份公私钥对在一定时间内无法被破解。

# 参考资料

- 阮一峰的博文：[RSA算法原理（二）](http://www.ruanyifeng.com/blog/2013/07/rsa_algorithm_part_two.html)
- [密码学基础1：RSA算法原理全面解析](https://www.jianshu.com/p/6aa7b59be872)
