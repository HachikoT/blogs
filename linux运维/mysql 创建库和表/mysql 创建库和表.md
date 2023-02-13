- [数据库](#数据库)
  - [创建数据库](#创建数据库)
  - [查看数据库](#查看数据库)
  - [删除数据库](#删除数据库)
- [数据表](#数据表)
  - [创建表](#创建表)
  - [查看表](#查看表)
  - [删除表](#删除表)
  - [清除表数据](#清除表数据)

# 数据库

## 创建数据库

语法

```bash
CREATE DATABASE [IF NOT EXISTS] <数据库名>
[[DEFAULT] CHARACTER SET <字符集名>] 
[[DEFAULT] COLLATE <校对规则名>];
```

示例

```bash
CREATE DATABASE userdb CHARACTER SET utf8 COLLATE utf8_general_ci;
```

这里常见的校对规则有

- `utf8_general_ci`：支持中英文的数字大小的比较，同时不区分大小写。
- `utf8_general_cs`：同上，区分大小写。
- `utf8_unicode_ci`：适用于德语，法语，俄语等语言需求，性能会稍稍低一点。

## 查看数据库

查看有哪些数据库：

```bash
SHOW DATABASES;
```

查看具体数据库的建表语句：

```bash
SHOW CREATE DATABASE userdb;
```

进入数据库：

```bash
USE userdb;
```

## 删除数据库

```bash
DROP DATABASE [if EXISTS] userdb;
```

# 数据表

## 创建表

语法

```bash
CREATE TABLE [IF NOT EXISTS] <表名> ([表定义选项])[表选项];
```

示例

```bash
CREATE TABLE employee(
    id INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(25) NOT NULL COMMENT '名字',
    gender VARCHAR(10) NOT NULL COMMENT '性别',
    salary FLOAT NOT NULL COMMENT '薪水',
    PRIMARY KEY(id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

## 查看表

查看建表语句

```bash
SHOW CREATE TABLE userdb;
```

查看表结构

```bash
DESC userdb;
```

## 删除表

```bash
DROP TABLE [IF EXISTS] userdb;
```

## 清除表数据

```bash
DELETE FROM userdb;
TRUNCATE TABLE userdb;
```

- `delete from`是删除表数据，对于auto_increment的字段id还是继续增加。而`truncate table`相当于保留了表的结构而重新建立了一张同样的新表。
- 效率上`truncate`比`delete`快。但`truncate`删除后不记录mysql日志，不可以恢复数据。
