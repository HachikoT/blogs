- [C文件操作](#c文件操作)
  - [打开文件](#打开文件)
  - [设置文件当前位置](#设置文件当前位置)
  - [读取文件](#读取文件)
  - [写入文件](#写入文件)
- [C++文件操作](#c文件操作-1)
  - [打开文件](#打开文件-1)
  - [设置文件当前位置](#设置文件当前位置-1)
  - [读取文件](#读取文件-1)
- [linux文件操作](#linux文件操作)
  - [打开文件](#打开文件-2)
  - [设置文件当前位置](#设置文件当前位置-2)
  - [读取文件](#读取文件-2)
  - [写入文件](#写入文件-1)
- [参考资料](#参考资料)

# C文件操作

## 打开文件

使用函数`fopen`来打开文件，函数原型如下。

```c
#include <stdio.h>

FILE *fopen( const char *restrict filename, const char *restrict mode );
```

打开失败返回`NULL`指针。

打开模式`mode`选项含义如下表。

| 模式  |               含义               |     当文件存在     | 当文件不存在 |
| :---: | :------------------------------: | :----------------: | :----------: |
|  `r`  |           打开文件来读           |   从文件开头读取   |   打开失败   |
|  `w`  |           打开文件来写           |    清空文件内容    |   创建文件   |
|  `a`  |           追加文件内容           |  从文件末尾开始写  |   创建文件   |
| `r+`  |          打开文件来读写          |   从文件开头读写   |   打开失败   |
| `w+`  |          打开文件来读写          |    清空文件内容    |   创建文件   |
| `a+`  |          打开文件来读写          | 从文件末尾开始读写 |   创建文件   |
|  `x`  | 和`w`，`a`结合表示文件存在则报错 |                    |              |

读写完之后，记得关闭文件来释放资源。

```c
#include <stdio.h>

int fclose( FILE *stream );
```

## 设置文件当前位置

使用函数`fseek`来设置文件当前位置。函数原型如下。

```c
#include <stdio.h>

int fseek( FILE *stream, long offset, int origin );
```

返回0表示设置成功，其他值表示失败。

`origin`来指定文件当前位置，支持三个值。`offset`则是在`origin`的基础上加上的偏移量。

|    值    |     含义     |
| :------: | :----------: |
| SEEK_SET |   文件开头   |
| SEEK_CUR | 当前文件位置 |
| SEEK_END |   文件末尾   |

## 读取文件

通过`fread`函数来读取文件，函数原型如下。

```c
#include <stdio.h>

size_t fread( void *restrict buffer, size_t size, size_t count, FILE *restrict stream );
```

返回实际读出对象个数，如果小于`count`那么可能是遇到错误，或者读完文件了，需要使用`ferror`和`feof`来判断具体是哪种情况。

## 写入文件

通过`fwrite`函数来读取文件，函数原型如下。

```c
size_t fwrite( const void *restrict buffer, size_t size, size_t count, FILE *restrict stream );
```

成功返回写入的对象数，小于传入的`count`，则发生了错误。

# C++文件操作

## 打开文件

首先需要创建文件io流对象。

```cpp
#include <fstream>

std::ifstream   // 输入文件流
std::ofstream   // 输出文件流
std::fstream    // 输入输出文件流
```

然后可以对文件流对象进行打开文件操作，将文件和文件流对象关联起来。

```cpp
void open( const std::string &filename, ios_base::openmode mode = ios_base::in );
```

需要调用`is_open`来判断是否成功打开文件。

```cpp
bool is_open() const;
```

打开模式`mode`选项含义如下表。

|        模式        |          含义          |
| :----------------: | :--------------------: |
|  `std::ios::app`   | 追加写，寻址到文件末尾 |
| `std::ios::binary` |    以二进制模式打开    |
|   `std::ios::in`   |         读文件         |
|  `std::ios::out`   |         写文件         |
| `std::ios::trunc`  |      清空文件内容      |
|  `std::ios::ate`   |     寻址到文件末尾     |

读写完之后，可以手动关闭文件，也可以让对象析构的时候自动释放。

```cpp
void close();
```

## 设置文件当前位置

调用`seekg`函数，原型如下。

```cpp
basic_istream& seekg( off_type off, std::ios_base::seekdir dir );
```

支持三种位置。

|       值        |     含义     |
| :-------------: | :----------: |
| `std::ios::beg` |   文件开头   |
| `std::ios::cur` | 当前文件位置 |
| `std::ios::end` |   文件末尾   |

## 读取文件

调用`read`函数，原型如下。

```cpp
basic_istream& read( char_type* s, std::streamsize count );
```

判断出错或者读取完文件需要用`fail`和`eof`成员函数来判断。

# linux文件操作

## 打开文件

使用`open`函数打开文件，原型如下。

```c
#include <fcntl.h>

int open(const char *path, int oflag, ... /* mode_t mode */ );
```

`mode`是可选的，用来表示文件不存在需要创建的时候新文件的读写权限。

打开失败返回-1，打开成功返回文件描述符。

打开选项`oflag`选项含义如下表。

|  打开选项   |                 含义                 |
| :---------: | :----------------------------------: |
|  O_RDONLY   |             打开文件来读             |
|  O_WRONLY   |             打开文件来写             |
|   O_RDWR    |            打开文件来读写            |
|   O_EXEC    |            打开可执行文件            |
|  O_SEARCH   |               打开目录               |
|  O_APPEND   |             追加文件内容             |
|  O_CLOEXEC  |                                      |
|   O_CREAT   | 文件不存在则创建，需要传入参数`mode` |
| O_DIRECTORY |         如文件不是目录则报错         |
|   O_EXCL    | 和O_CREAT一起用，表示文件存在则报错  |
|  O_NOCTTY   |                                      |
| O_NOFOLLOW  |        如文件是链接文件则报错        |
| O_NONBLOCK  |                                      |
|   O_SYNC    |                                      |
|   O_TRUNC   |        文件存在先清空文件内容        |
| O_TTY_INIT  |                                      |
|   O_DSYNC   |                                      |
|   O_RSYNC   |                                      |

读写完之后，通过`close`关闭文件释放资源。

```c
#include <unistd.h>

int close(int fd);
```

## 设置文件当前位置

使用函数`lseek`来设置文件当前位置。函数原型如下。

```c
#include <unistd.h>

off_t lseek(int fd, off_t offset, int whence);
```

成功返回新的文件位置偏移值，失败返回-1。

`whence`来指定文件当前位置，支持三个值。`offset`则是在`whence`的基础上加上的偏移量。

|    值    |     含义     |
| :------: | :----------: |
| SEEK_SET |   文件开头   |
| SEEK_CUR | 当前文件位置 |
| SEEK_END |   文件末尾   |

## 读取文件

使用函数`read`函数来读取文件内容，函数原型如下。

```c
#include <unistd.h>

ssize_t read(int fd, void *buf, size_t nbytes);
```

成功返回实际读取到的内容的字节大小，读完文件了那么返回0，错误返回-1。

## 写入文件

使用函数`fwrite`函数来写入文件内容，函数原型如下。

```c
#include <unistd.h>

ssize_t write(int fd, const void *buf, size_t nbytes);
```

写入成功返回写入的字节数，失败返回-1。


# 参考资料

- cppreference
