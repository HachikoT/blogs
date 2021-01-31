# RSA

1977年，三位数学家Rivest、Shamir和Adleman设计了一种算法，可以实现非对称加密。算法用他们三个人的名字命名，叫做RSA算法。
RSA算法的安全性是基于判断一个数是否为质数很简单，但是要对一个数进行质因子分解很困难这样一个事实。

# RSA公私钥生成步骤

1. **找两个大的质数$P$和$Q$，越大越好，计算$P$和$Q$的乘积$N$**
$$N=P\times Q$$
2. **计算N的欧拉函数$\varphi(N)$，令其值为$M$**
$$M=\varphi(N)=(P-1)(Q-1)$$
3. **找一个和$M$互质的整数$E$**
$$gcd(E,M)=1$$
4. **计算整数$E$模$M$的乘法逆元$D$**
$$E\times D\equiv 1\,mod\,M$$

最终，将$(N,D)$作为私钥，$(N,E)$作为公钥

# RSA加解密步骤

下面是用私钥加密，公钥解密的过程：

- **加密**：
$$X^D\,mod\,N=Y$$
- **解密**：
$$Y^E\,mod\,N=X$$

这里可以还原出加密的数据是因为
$$\begin{align}
(X^D)^E &\equiv X^{D\times E}\,mod\,N\\
&\equiv X^{kM}X\,mod\,N\\
&\equiv X^{k\varphi(N)}X\,mod\,N\\
&\equiv X\,mod\,N\\
\end{align}$$
上面的推导中利用了欧拉定理：$X^{\varphi(N)}\equiv 1\,mod\,N$，但这需要$X$与$N$互质
当$X$不与$N$互质时，只可能$X=tP$或者$X=tQ$，这里推导$X=tP$的情况，这里有$tP$与$Q$一定是互质的
$$\begin{align}
(tP)^{DE} &\equiv (tP)^{k\varphi(P)\varphi(Q)}(tP)\,mod\,Q\\
&\equiv tP\,mod\,Q\\
\end{align}$$
推出
$$(tP)^{DE}=t'Q+tP$$
由于$P|t'Q$所以
$$(tP)^{DE}=t''PQ+tP$$
上式等价于
$$(tP)^{DE}\equiv tP\,mod\,N$$
$$(X)^{DE}\equiv X\,mod\,N$$

# RSA安全性

RSA公私钥生成过程中可知，要从$E$推导出$D$或者相反，必须要知道数$N$的欧拉函数$\varphi(N)=(P-1)(Q-1)$
所以RSA算法的安全性就等价于对大整数$N$进行因式分解找出大质数$P$和$Q$，而目前质因子分解并没有多项式解法，所以可能保证一份公私钥对在一定时间内无法被破解
