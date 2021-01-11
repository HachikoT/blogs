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

EPEL（Extra Packages for Enterprise Linux）是由Fedora社区打造，为RHEL及衍生发行版如CentOS等提供高质量软件包的项目，里面包含了许多基本源里没有的软件源，比如`cmake3，golang，docker-compose，nginx`等等。安装命令如下：

```sh
sudo yum install -y epel-release
```
