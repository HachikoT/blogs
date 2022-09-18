- [linux echo 命令](#linux-echo-命令)
  - [语法](#语法)
  - [常用参数](#常用参数)
  - [使用场景](#使用场景)
    - [搭配base64命令进行编码](#搭配base64命令进行编码)

# linux echo 命令

`echo`命令一般用来在shell脚本中输出文本，输出简短的文本信息。

## 语法

```bash
echo [SHORT-OPTION]... [STRING]...
```

## 常用参数

- `-n`：输出的文本信息末尾不添加换行符。

## 使用场景

### 搭配base64命令进行编码

这里强烈建议给`echo`命令加上`-n`参数，因为不带的话将把换行符也作为输入编码到base64字符串里面了。

```bash
[rc@localhost ~]$ echo -n "secret" | base64
c2VjcmV0
```
