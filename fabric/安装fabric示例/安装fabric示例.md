# bootstrap.sh脚本

fabric官方提供了脚本，可以一键下载fabric-samples仓库，二进制工具，fabric相关镜像，下面安装fabric v1.4.4版本的示例：

```sh
curl https://raw.githubusercontent.com/hyperledger/fabric/v1.4.4/scripts/bootstrap.sh | bash -s
```

但是这个版本的脚本好像过期了，仓库和二进制工具的下载连接有误，这里可以手动下载一下：

```sh
# 下载fabric-samples仓库
git clone git@github.com:hyperledger/fabric-samples.git
cd fabric-samples
git checkout v1.4.4

# 下载二进制工具
ARCH=$(echo "$(uname -s|tr '[:upper:]' '[:lower:]'|sed 's/mingw64_nt.*/windows/')-$(uname -m | sed 's/x86_64/amd64/g')")
VERSION=1.4.4
CA_VERSION=1.4.4
BINARY_FILE=hyperledger-fabric-${ARCH}-${VERSION}.tar.gz
CA_BINARY_FILE=hyperledger-fabric-ca-${ARCH}-${CA_VERSION}.tar.gz
curl -L --retry 5 --retry-delay 3 https://github.com/hyperledger/fabric/releases/download/v${VERSION}/${BINARY_FILE} | tar xz
curl -L --retry 5 --retry-delay 3 https://github.com/hyperledger/fabric-ca/releases/download/v${CA_VERSION}/${CA_BINARY_FILE} | tar xz
```

对于mac是这个：

```sh
curl -L --retry 5 --retry-delay 3 https://github.com/hyperledger/fabric/releases/download/v1.4.4/hyperledger-fabric-darwin-amd64-1.4.4.tar.gz | tar xz
curl -L --retry 5 --retry-delay 3 https://github.com/hyperledger/fabric-ca/releases/download/v1.4.4/hyperledger-fabric-ca-darwin-amd64-1.4.4.tar.gz | tar xz
```
