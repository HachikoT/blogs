- [linux环境通过安装包安装](#linux环境通过安装包安装)

# linux环境通过安装包安装

这里参考官方的安装步骤[Download and install golang](https://golang.google.cn/doc/install)

1. 下载安装包

```bash
wget https://golang.google.cn/dl/go1.25.4.linux-amd64.tar.gz
```

2. 解压安装包到`/usr/local`目录下

```bash
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.25.4.linux-amd64.tar.gz
```

3. 将`/usr/local/go/bin`目录添加到`PATH`环境变量中，这里添加到`/etc/profile`文件中（登入式shell加载配置）

```bash
sudo vi /etc/profile
# 添加以下配置
export PATH=$PATH:/usr/local/go/bin
```

4. 检查安装结果，执行`go env`，可以看到go已经自动识别出`GOPATH`和`GOROOT`的配置了

```bash
rc@rc-virtual-machine:~$ go env
AR='ar'
CC='gcc'
CGO_CFLAGS='-O2 -g'
CGO_CPPFLAGS=''
CGO_CXXFLAGS='-O2 -g'
CGO_ENABLED='0'
CGO_FFLAGS='-O2 -g'
CGO_LDFLAGS='-O2 -g'
CXX='g++'
GCCGO='gccgo'
GO111MODULE=''
GOAMD64='v1'
GOARCH='amd64'
GOAUTH='netrc'
GOBIN=''
GOCACHE='/home/rc/.cache/go-build'
GOCACHEPROG=''
GODEBUG=''
GOENV='/home/rc/.config/go/env'
GOEXE=''
GOEXPERIMENT=''
GOFIPS140='off'
GOFLAGS=''
GOGCCFLAGS='-fPIC -m64 -fno-caret-diagnostics -Qunused-arguments -Wl,--no-gc-sections -fmessage-length=0 -ffile-prefix-map=/tmp/go-build2950718997=/tmp/go-build -gno-record-gcc-switches'
GOHOSTARCH='amd64'
GOHOSTOS='linux'
GOINSECURE=''
GOMOD='/dev/null'
GOMODCACHE='/home/rc/go/pkg/mod'
GONOPROXY=''
GONOSUMDB=''
GOOS='linux'
GOPATH='/home/rc/go'
GOPRIVATE=''
GOPROXY='https://proxy.golang.org,direct'
GOROOT='/usr/local/go'
GOSUMDB='sum.golang.org'
GOTELEMETRY='local'
GOTELEMETRYDIR='/home/rc/.config/go/telemetry'
GOTMPDIR=''
GOTOOLCHAIN='auto'
GOTOOLDIR='/usr/local/go/pkg/tool/linux_amd64'
GOVCS=''
GOVERSION='go1.25.4'
GOWORK=''
PKG_CONFIG='pkg-config'
```
