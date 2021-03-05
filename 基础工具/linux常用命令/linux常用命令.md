# date

date命令可以用来显示或设定系统的日期与时间，在显示方面，使用者可以设定欲显示的格式。

- **使用场景1**：备份文件的时候代替手动输入时间

```sh
cp file.txt file.txt.`date +%Y%m%d%H%M`
```

# screen

screen可以为你在远端主机保留你当前的会话session，这样就不用怕连接中断了：

```sh
screen -S yourname           # 新建一个叫yourname的session
screen -ls                   # 列出当前所有的session
screen -r yourname           # 回到yourname这个session
screen -d yourname           # 远程detach某个session    # detach快捷键 ctrl a + d
screen -S yourname -X quit   # 删除叫yourname的session
```


# 参考资料

- [Linux--screen远程必备](https://blog.csdn.net/qq_34243930/article/details/106771285)
