- [viper](#viper)
  - [从配置文件读取示例](#从配置文件读取示例)
  - [从环境变量中读取](#从环境变量中读取)
  - [从命令行中读取](#从命令行中读取)
- [参考资料](#参考资料)

# viper

viper是go语言中用来读取配置的第三方包，支持的功能很丰富。

- 支持设置默认值
- 支持JSON、TOML、YAML、HCL、INI、envfile和Java properties格式
- 从环境变量中读取配置
- 从命令行参数读取配置
- 从远程配置系统（etcd或Consul）读取并监控配置变化

viper支持在多个地方设置，使用下面的顺序依次读取。

- 调用Set显示设置的
- 命令行选项
- 环境变量
- 配置文件
- 默认值

## 从配置文件读取示例

配置文件为`app.conf`。

```ini
[mysql]
host = 127.0.0.1
port = 3306
user = root
password = 12341234
```

读取配置代码如下。

```go
package main

import (
	"fmt"

	"github.com/spf13/viper"
)

func main() {
	viper.AddConfigPath(".")        // 配置文件搜寻路径，可以添加多个
	viper.SetConfigName("app.conf") // 配置文件名
	viper.SetConfigType("ini")      // 配置文件格式
	err := viper.ReadInConfig()
	if err != nil {
		panic(fmt.Errorf("read config file failed: %v", err))
	}

	fmt.Println(viper.Get("mysql.user"))
}

```

## 从环境变量中读取

下面的程序中可以正确识别到`TEST_MYSQL_USER`这个环境变量，并将其覆盖`mysql.user`这个配置。

```go
package main

import (
	"fmt"
	"strings"

	"github.com/spf13/viper"
)

func main() {
	viper.AddConfigPath(".")
	viper.SetConfigName("app.conf")
	viper.SetConfigType("ini")
	err := viper.ReadInConfig()
	if err != nil {
		panic(fmt.Errorf("read config file failed: %v", err))
	}

	viper.SetEnvPrefix("TEST")                             // 设置要读取的环境变量的前缀
	viper.SetEnvKeyReplacer(strings.NewReplacer(".", "_")) // 可以识别环境变量中的下划线
	viper.AutomaticEnv()                                   // 读取环境变量

	fmt.Println(viper.Get("mysql.user"))
}

```

## 从命令行中读取

下面的程序可以正确识别`go run main.go --mysql.user rcc`中，命令行指定的`mysql.user`的配置。

```go
package main

import (
	"fmt"
	"strings"

	"github.com/spf13/pflag"
	"github.com/spf13/viper"
)

func main() {
	viper.AddConfigPath(".")
	viper.SetConfigName("app.conf")
	viper.SetConfigType("ini")
	err := viper.ReadInConfig()
	if err != nil {
		panic(fmt.Errorf("read config file failed: %v", err))
	}

	viper.SetEnvPrefix("TEST")
	viper.SetEnvKeyReplacer(strings.NewReplacer(".", "_"))
	viper.AutomaticEnv()

	pflag.String("mysql.user", "root", "mysql user")
	pflag.Parse()
	viper.BindPFlags(pflag.CommandLine) // 读取pflag中解析到的配置

	fmt.Println(viper.Get("mysql.user"))
}

```

# 参考资料

- [Go 每日一库之 viper](https://studygolang.com/articles/26215?fr=sidebar)
- [viper](https://pkg.go.dev/github.com/spf13/viper@v1.10.1#section-readme)

