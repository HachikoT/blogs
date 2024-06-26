- [capabilities](#capabilities)
  - [线程的capabilities](#线程的capabilities)
  - [文件的capabilities](#文件的capabilities)
  - [`execve`启动的子进程的权限计算规则](#execve启动的子进程的权限计算规则)
  - [root用户`execve`启动的子进程的权限计算规则](#root用户execve启动的子进程的权限计算规则)
  - [root和非root用户切换时进程权限变化规则](#root和非root用户切换时进程权限变化规则)
- [参考](#参考)

# capabilities

`capabilities`是一种细粒度的权限控制机制,用于将`root`用户的特权划分为更小、更具体的单元，如文件读写、网络管理、系统时间修改等。

## 线程的capabilities

线程的属性中包含5种权限集合：

1. `Permitted`：是`Effective`权限的超集，对于没有`CAP_SETPCAP`权限的线程来说，也是`Inheritable`权限的超集。可以通过`capset`系统调用添加或者删除`Effective`或`Inheritable`中的权限。
2. `Inheritable`：通过`execve`执行二进制文件启动子进程时，`Inheritable`权限将完全继承给子进程，并且当二进制文件对应的`Inheritable`权限位被置为1时，该位置的权限也将被添加到子进程的`Permitted`权限中。
3. `Effective`：线程当前生效权限。
4. `Bounding`：是`Inheritable`权限的超集。对于不在`Bounding`集合中的权限，即使在`Permitted`集合中，也不能添加到`Inheritable`中。对于在`Bounding`集合中的权限，但是不在`Permitted`集合中，那么需要拥有`CAP_SETPCAP`权限才可以添加到`Inheritable`中。
5. `Ambient`：用来弥补`Inheritable`权限的不足，当执行没有特权的文件时（没有设置suid，没有设置capabilities的文件），`Ambient`权限会变为子进程的`Effective`权限来生效，这样当非root用户执行脚本的时候也可以将特权生效下去。可以通过`prctl`系统调用添加或者删除`Ambient`中的权限。

## 文件的capabilities

文件的权限被保存在文件的扩展属性中，可以通过`getcap`和`setcap`来查看和设置文件的权限（设置需要`CAP_SETFCAP`权限）。

1. `Permitted`：在文件被执行时，和线程的`Bounding`权限计算交集，添加到子进程的`Permitted`权限中。
2. `Inheritable`：在文件被执行时，和线程的`Inheritable`权限计算交集，添加到子进程的`Permitted`权限中。
3. `Effective`：这不是集合，而是一个标记，决定了起子进程的时候，计算子进程权限的机制。

## `execve`启动的子进程的权限计算规则

`P`表示父进程的权限，`F`表示文件的权限，`P’`表示子进程的权限，文件是否是特权的取决于有没有设置suid或者设置capabilities。

```text
P’(ambient)     = (file is privileged) ? 0 : P(ambient)
P’(permitted)   = (P(inheritable) & F(inheritable)) |
                  (F(permitted) & P(bounding)) | P’(ambient)
P’(effective)   = F(effective) ? P’(permitted) : P’(ambient)
P’(inheritable) = P(inheritable)    [i.e., unchanged]
P’(bounding)    = P(bounding)       [i.e., unchanged]
```

## root用户`execve`启动的子进程的权限计算规则

平时用root用户去执行没有设置capabilities的二进制，也可以通过权限校验，是因为root用户的计算规则是有特殊处理的。

- 当执行用户是root的时候，文件的`Permitted`和`Inheritable`被认为是全1。
- 当执行用户是root的时候，文件的`Effective`被认为是全1。

所以，对于root用户计算规则简化为如下

```text
P’(permitted)   = P(inheritable) | P(bounding)
P’(effective)   = P’(permitted)
```

## root和非root用户切换时进程权限变化规则

- root切非root，`Permitted`和`Effective`和`Ambient`将被清空。
- 非root切root，`Permitted`被赋值到`Effective`。

# 参考

- [man capabilities](https://man.cx/capabilities)
