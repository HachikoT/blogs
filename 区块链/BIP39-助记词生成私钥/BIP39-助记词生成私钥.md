# BIP39

二进制或16进制的私钥很难辨别和记忆，所以BIP39（Bitcoin Improvement Proposals 0039）提案提出了用助记词来保存私钥的方法。
助记词是由十二到二十四个单词组成的密码，它被用于生成区块链账户系统中512位的种子(seed)，而种子可以生成私钥(prikey)，由私钥可以导出公钥(pubkey)，公钥经过变换后可以得到我们经常在区块链账本上看到的地址(address)。

助记词生成步骤：

1. 生成指定位数[128,256]的随机数熵（entropy）。
2. 计算随机数的sha256哈希值，取前面指定几位数（随机数熵位数/32）追加到随机数后面作为校验和。
3. 将随机数每11位分为一组，每一组的范围是[0,2047]，根据这个值去助记词表查，得到助记词句子。

助记词表，一个好的助记词表应该满足下面的条件：

- 助记词的前缀尽可能不同。
- 尽量不要出现相似的助记词，比如“build”和“built”。
- 助记词表应该是排好序的，方便查找。

根据助记词生成私钥步骤：

1. 使用PBKDF2函数来生成seed，用户还可以给助记词句子加一个passphrase，如果没有那么默认为空。
2. 再将生成的seed传入bip32的NewMasterKey函数，便可以得到密钥。

# 参考资料

- [Mnemonic code for generating deterministic keys](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki)
- [BIP39 助记词生成过程详解](https://cloud.tencent.com/developer/news/327709)
- [PBKDF2 算法概述](http://www.voidcn.com/article/p-vdtfkabe-nq.html)
- [Hierarchical Deterministic Wallets](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)
