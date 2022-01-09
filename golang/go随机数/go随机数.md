- [math/rand](#mathrand)
- [crypto/rand](#cryptorand)
- [linux真随机数](#linux真随机数)
- [参考资料](#参考资料)

# math/rand

`math/rand`实现了伪随机数算法，和其它的编程语言类似，操作逻辑都是先设置随机数种子，然后再获取随机数序列。这样每次生成的随机数序列都是不一样的。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	for i := 0; i < 10; i++ {
		// 生成0～99的随机数
		fmt.Println(rand.Intn(100))
	}
}

```

并且go还保证了上述的代码生成随机数是线程安全的，因为里面的操作加锁了，但可能有很少的场景强调效率，也可以自己创建一个伪随机源，这样就不会加锁了。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	r := rand.New(rand.NewSource(time.Now().UnixNano()))
	for i := 0; i < 10; i++ {
		// 生成0～99的随机数
		fmt.Println(r.Intn(100))
	}
}

```

# crypto/rand

上面`math/rand`包生成的随机数是伪随机的，可以根据上一个随机数的值计算出下一个，只是在总体的分布上是均匀的，来模拟随机。
但有些场景需要密码学安全的随机数，也就是无法预测的随机数，所以go语言提供了`crypto/rand`包，里面提供了真随机数产生接口，满足密码学安全的需求。

```go
package main

import (
	"crypto/rand"
	"fmt"
)

func main() {
	b := make([]byte, 10)
    // Reader is a global, shared instance of a cryptographically
    // secure random number generator.
    //
    // On Linux and FreeBSD, Reader uses getrandom(2) if available, /dev/urandom otherwise.
    // On OpenBSD, Reader uses getentropy(2).
    // On other Unix-like systems, Reader reads from /dev/urandom.
    // On Windows systems, Reader uses the CryptGenRandom API.
    // On Wasm, Reader uses the Web Crypto API.
	rand.Read(b)
	fmt.Println(b)
}
```

# linux真随机数

为了获得真正意义上的随机数，需要一个外部的噪声源。Linux内核找到了一个完美的噪声源产生者--就是使用计算机的人。
内核根据非确定性的设备事件维护着一个熵池，池中的数据是完全随机的。当有新的设备事件到来，内核会估计新加入的数据的随机性，当我们从熵池中取出数据时，内核会减少熵的估计值。
`/dev/random`和`/dev/urandom`这两个特殊设备都是字符型设备。我们可以在用户空间通过read系统调用读这两个设备文件以此获取随机数。这两个设备文件的区别在于：如果内核熵池的估计值为0时，`/dev/random`将被阻塞，而`/dev/urandom`不会有这个限制。

# 参考资料

- [从Linux内核中获取真随机数](https://www.cnblogs.com/bigship/archive/2010/04/04/1704228.html)
- [关于 /dev/urandom 的流言终结](https://zhuanlan.zhihu.com/p/64680713)
