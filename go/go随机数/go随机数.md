# math/rand

`math/rand`实现了伪随机数算法，并且该方法是线程安全的，里面的操作都加锁了：

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	fmt.Println(rand.Intn(100))
}

```

也可以自己创建一个伪随机源，这样就不会加锁了：

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	r := rand.New(rand.NewSource(time.Now().UnixNano()))
	fmt.Println(r.Intn(100))
}

```

# crypto/rand

`crypto/rand`提供了真随机数，满足密码学安全：

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
