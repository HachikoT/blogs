# 终端命令提示符设置

在终端命令界面，最左侧会显示提示符，可以通过设置`PS1`（Prompt Sign）环境变量来改变格式：`sudo vi /etc/profile`，设置如下

```sh
# PS1
export PS1='[\u@\h]\w\$'
```

| 参数 | 描述 | 参数 | 描述 |
| :---: | :---: | :---: | :---: |
| \H | 显示主机名和域名 | \h | 只显示主机名 |
| \W | 利用basename取得工作目录名称，所以只会列出最后一个目录 | \w | 完整的工作目录名称。家目录会以~代替 |
| \T | 显示时间为12小时格式 | \t | 显示时间为24小时格式，如：HH:MM:SS |
| \d | 代表日期，格式为weekday month date，例如：“Mon Aug 1” | \A | 显示时间为24小时格式：HH:MM |
| \u | 当前用户的账号名称 | \\$ | 提示字符，如果是root时，提示符为“#”，普通用户则为“$” |
| \v | BASH的版本信息 | \\# | 下达的第几个命令 |
| \\! | 命令行动态统计历史命令次数 |||
| [ | 字符“[” | ] | 字符“]” |

# 安装EPEL源

EPEL（Extra Packages for Enterprise Linux）是由Fedora社区打造，为RHEL及衍生发行版如CentOS等提供高质量软件包的项目，里面包含了许多基本源里没有的软件源，比如`cmake3，golang，docker-compose`等等。安装命令如下：

```sh
sudo yum install -y epel-release
```

# 安装常用软件

- man手册：安装centos7之后，man一些C库函数和pthread函数都查找不到，也就是man手册不全，这时需要安装`man-pages`，`sudo yum install -y man-pages`
- 命令行自动补全功能：在终端界面需要使用`tab`键来进行命令的自动补全，需要安装`sudo yum install -y bash-completion`
- c/c++环境：`sudo yum install -y gcc gcc-c++ gdb`
- make/cmake：`sudo yum install -y make cmake3`
- git/wget/curl：`sudo yum install -y git wget curl`
- 压缩/解压缩：`sudo yum install -y zip unzip bzip2`
- tree/jq：`sudo yum install -y tree jq`

安装全部：

```sh
sudo yum install -y man-pages bash-completion gcc gcc-c++ gdb make cmake3 git wget curl zip unzip bzip2 tree jq
```
