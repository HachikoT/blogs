# uuid

这里使用google的库：

```go
package main

import (
	"fmt"

	guuid "github.com/google/uuid"
)

func main() {
	fmt.Println(genUUIDV4())
}

func genUUIDV4() string {
	uuid, err := guuid.NewRandom()
	if err != nil {
		return ""
	}
	return uuid.String()
}

```
