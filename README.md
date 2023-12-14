# linux 系统编程

## 文件属性

| 字符 | 说明               | 常量      | 测试宏         | 文件类型         |
|------|--------------------|-----------|----------------|------------------|
| b    | 块设备文件         | S_IFBLK   | S_ISBLK()      | 块设备文件       |
| c    | 字符设备文件       | S_IFCHR   | S_ISCHR()      | 字符设备文件     |
| d    | 目录文件           | S_IFDIR   | S_ISDIR()      | 目录文件         |
| -    | 普通文件           | S_IFREG   | S_ISREG()      | 普通文件         |
| l    | 符号链接文件       | S_IFLNK   | S_ISLNK()      | 符号链接文件     |
| s    | 套接字文件         | S_IFSOCK  | S_ISSOCK()     | 套接字文件       |
| p    | 管道文件           | S_IFIFO   | S_ISFIFO()     | 管道文件         |


| 函数            | 头文件         | 作用                                      |
|-----------------|--------------|-------------------------------------------|
| `int link(const char *oldpath, const char *newpath);` | `#include <unistd.h>` | 创建硬链接，将 `oldpath` 的文件链接到 `newpath`。   |
| `int unlink(const char *pathname);` | `#include <unistd.h>` | 删除文件或目录的链接，减少链接计数。  |
| `int symlink(const char *oldpath, const char *newpath);` | `#include <unistd.h>` | 创建符号链接，将 `newpath` 链接到 `oldpath`。|
| `ssize_t readlink(const char *path, char *buf, size_t bufsiz);` | `#include <unistd.h>` | 读取符号链接 `path` 的内容到缓冲区 `buf`，最多读取 `bufsiz` 个字节。返回实际读取的字节数。|
| `int stat(const char *path, struct stat *buf);` | `#include <sys/types.h>`<br>`#include <sys/stat.h>`<br>`#include <unistd.h>` | 获取文件或目录的统计信息，存储在 `buf` 结构体中。           |
| `int fstat(int fd, struct stat *buf);`       | `#include <sys/types.h>`<br>`#include <sys/stat.h>` | 获取文件描述符 `fd` 所指向文件的统计信息，存储在 `buf` 结构体中。|
| `int lstat(const char *path, struct stat *buf);` | `#include <sys/types.h>`<br>`#include <sys/stat.h>`<br>`#include <unistd.h>` | 获取符号链接文件 `path` 的统计信息，存储在 `buf` 结构体中。   |
| `int chown(const char *path, uid_t owner, gid_t group);` | `#include <unistd.h>` | 修改文件或目录 `path` 的所有者和所属组。              |
| `int fchown(int fd, uid_t owner, gid_t group);` | `#include <unistd.h>` | 修改文件描述符 `fd` 所指向文件的所有者和所属组。      |
| `int lchown(const char *path, uid_t owner, gid_t group);` | `#include <unistd.h>` | 修改符号链接文件 `path` 的所有者和所属组，而不追踪链接。|
| `int chmod(const char *path, mode_t mode);` | `#include <sys/stat.h>` | 修改文件或目录 `path` 的权限模式。                 |
| `int fchmod(int fd, mode_t mode);`      | `#include <sys/stat.h>` | 修改文件描述符 `fd` 所指向文件的权限模式。         |
| `int truncate(const char *path, off_t length);` | `#include <unistd.h>`<br>`#include <sys/types.h>` | 将文件或目录 `path` 截断到指定的 `length` 字节。   |
| `int ftruncate(int fd, off_t length);`        | `#include <unistd.h>` | 将文件描述符 `fd` 所指向的文件截断到指定的 `length` 字节。|
| `int rename(const char *oldpath, const char *newpath);` | `#include <stdio.h>` | 重命名文件或目录，将 `oldpath` 改为 `newpath`。   |
| `int utime(const char *filename, const struct utimbuf *times);` | `#include <sys/types.h>`<br>`#include <utime.h>` | 修改文件的访问时间和修改时间，通过 `times` 结构体传递时间信息。  |
| `int utimes(const char *filename, const struct timeval times[2]);` | `#include <sys/time.h>` | 修改文件的访问时间和修改时间，通过 `times` 数组传递时间信息。  |
| `int futimes(int fd, const struct timeval tv[2]);` | `#include <sys/time.h>` | 修改文件描述符 `fd` 所指向的文件的访问时间和修改时间，通过 `tv` 数组传递时间信息。  |
| `int lutimes(const char *filename, const struct timeval tv[2]);` | `#include <sys/time.h>` | 修改符号链接文件 `filename` 的访问时间和修改时间，通过 `tv` 数组传递时间信息。  |
| `int mkdir(const char *pathname, mode_t mode);` | `#include <sys/stat.h>`<br>`#include <sys/types.h>` | 创建一个新目录 `pathname`，具有指定的权限模式 `mode`。              |
| `DIR *opendir(const char *name);` | `#include <sys/types.h>`<br>`#include <dirent.h>` | 打开目录 `name`，返回指向目录流的指针 `DIR *`。                 |
| `struct dirent *readdir(DIR *dirp);` | `#include <dirent.h>` | 读取目录流 `dirp` 中的下一个目录项，返回指向 `struct dirent` 结构体的指针。|
| `char *dirname(char *path);`         | `#include <libgen.h>` | 返回路径 `path` 的目录部分，修改原始字符串以删除文件名部分。        |
| `char *basename(char *path);`        | `#include <libgen.h>` | 返回路径 `path` 的文件名部分，修改原始字符串以删除目录部分。        |

## I/O

### 标准I/O

| 模式  | 功能                                                                                       |
|------|--------------------------------------------------------------------------------------------|
| `r`  | 以只读的方式打开文件，文件必须存在。                                                      |
| `r+` | 以读写的方式打开文件，文件必须存在。                                                     |
| `w`  | 以只写的方式打开文件。如果文件不存在，则自动创建；如果文件存在，则截取文件的长度为 0，即清空文件中的数据。|
| `w+` | 以读写的方式打开文件。如果文件不存在，则自动创建；如果文件存在，则截取文件的长度为 0，即清空文件中的数据。|
| `a`  | 以只写的方式打开文件。如果文件不存在，则自动创建；如果文件存在，则追加到文件的末尾，即原有数据不清空，在数据末尾继续写入。|
| `a+` | 以读写的方式打开文件。如果文件不存在，则自动创建；如果文件存在，则追加到文件的末尾，即原有数据不清空，在数据末尾继续写入，但在数据开头读取。|

| 用途               | 对应文件描述符 | 宏定义           | 标准流指针     |
|--------------------|----------------|------------------|----------------|
| 标准输入           | 0              | `STDIN_FILENO`  | `stdin`        |
| 标准输出           | 1              | `STDOUT_FILENO` | `stdout`       |
| 标准错误输出       | 2              | `STDERR_FILENO` | `stderr`       |


| 函数                                           | 头文件              | 作用                                                           |
|------------------------------------------------|-------------------|----------------------------------------------------------------|
| `FILE *fopen(const char *path, const char *mode);` | `#include <stdio.h>` | 打开文件 `path`，返回文件流的指针 `FILE *`，使用指定的打开模式 `mode`。   |
| `FILE *fdopen(int fd, const char *mode);`        | `#include <stdio.h>` | 将文件描述符 `fd` 转换为文件流，返回文件流的指针 `FILE *`，使用指定的打开模式 `mode`。|
| `FILE *freopen(const char *path, const char *mode, FILE *stream);` | `#include <stdio.h>` | 关闭流 `stream`，将文件 `path` 以指定的模式 `mode` 重新打开，返回文件流的指针 `FILE *`。|
| `int fclose(FILE *fp);` | `#include <stdio.h>` | 关闭文件流 `fp`，释放相关资源。                                   |
| `void perror(const char *s);` | `#include <stdio.h>` | 打印与当前 `errno` 值关联的错误消息，以及可选的字符串 `s`。        |
| `char *strerror(int errnum);`     | `#include <string.h>` | 返回与错误码 `errnum` 相关的错误消息字符串。                   |
| `int fputc(int c, FILE *stream);` | `#include <stdio.h>` | 将字符 `c` 写入到文件流 `stream` 中，并返回写入的字符。       |
| `int fgetc(FILE *stream);`        | `#include <stdio.h>` | 从文件流 `stream` 中读取一个字符，并返回读取的字符。           |
| `int fputs(const char *s, FILE *stream);` | `#include <stdio.h>` | 将字符串 `s` 写入到文件流 `stream` 中，并返回非负值表示成功，若失败返回 `EOF`。|
| `char *fgets(char *s, int size, FILE *stream);` | `#include <stdio.h>` | 从文件流 `stream` 中读取最多 `size - 1` 个字符到字符串 `s` 中，直到遇到换行符、文件结束符或达到指定大小。返回读取的字符串，若失败或已到达文件结尾返回 `NULL`。|
| `size_t fwrite(const void *ptr, size_t size, size_t nmemb, FILE *stream);` | `#include <stdio.h>` | 将位于内存中的数据块 `ptr` 写入到文件流 `stream` 中，每个数据块的大小为 `size` 字节，共写入 `nmemb` 个数据块。返回实际写入的数据块数目。|
| `size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream);` | `#include <stdio.h>` | 从文件流 `stream` 中读取数据块到内存中的 `ptr`，每个数据块的大小为 `size` 字节，共读取 `nmemb` 个数据块。返回实际读取的数据块数目。|
| `int getchar(void);`                   | `#include <stdio.h>` | 从标准输入（标准输入流）中读取一个字符，并返回所读取的字符。    |
| `int putchar(int c);`              | `#include <stdio.h>` | 将字符 `c` 写入到标准输出（标准输出流），并返回写入的字符。   |
| `char *gets(char *s);`           | `#include <stdio.h>` | 从标准输入（标准输入流）中读取字符串，存储到字符数组 `s` 中，直到遇到换行符或文件结束符。返回成功读取的字符串，若失败返回 `NULL`。注意：`gets` 存在安全问题，不推荐使用。|
| `char *fgets(char *s, int size, FILE *stream);` | `#include <stdio.h>` | 从文件流 `stream` 中读取字符串，存储到字符数组 `s` 中，最多读取 `size - 1` 个字符，直到遇到换行符、文件结束符或达到指定大小。返回成功读取的字符串，若失败或已到达文件结尾返回 `NULL`。|
| `int puts(const char *s);`         | `#include <stdio.h>` | 将字符串 `s` 输出到标准输出（标准输出流），并追加换行符。返回非负值表示成功，若失败返回 `EOF`。|
| `int fflush(FILE *stream);`     | `#include <stdio.h>` | 刷新流 `stream` 的输出缓冲区。如果参数 `stream` 为 `NULL`，则刷新所有的输出缓冲区。返回非负值表示成功，若失败返回 `EOF`。|
| `void setbuf(FILE *stream, char *buf);`           | `#include <stdio.h>` | 设置文件流 `stream` 的缓冲区为 `buf`。若 `buf` 为 `NULL`，则禁用缓冲区，使用全局默认缓冲区。|
| `int setvbuf(FILE *stream, char *buf, int mode, size_t size);` | `#include <stdio.h>` | 设置文件流 `stream` 的缓冲区及其属性。`mode` 可以取 `_IOFBF`、`_IOLBF`、`_IONBF` 中的一个，分别表示全缓冲、行缓冲和无缓冲。`size` 表示缓冲区大小。若 `buf` 为 `NULL`，则函数会为缓冲区分配空间。返回非零值表示失败，零表示成功。|
| `int fseek(FILE *stream, long offset, int whence);` | `#include <stdio.h>` | 设置文件流 `stream` 的位置指示器，`offset` 是偏移量，`whence` 表示基准位置，可以取 `SEEK_SET`、`SEEK_CUR`、`SEEK_END` 中的一个。返回非零值表示失败，零表示成功。|
| `long ftell(FILE *stream);`           | `#include <stdio.h>` | 获取文件流 `stream` 的当前位置指示器的偏移量。返回当前位置的偏移量，若失败返回 `EOF`。|

| 函数原型                           | 所需头文件           | 函数参数    | 成功返回值 | 失败返回值 |
|------------------------------------|---------------------|-------------|------------|------------|
| `int scanf(const char *format, ...);` | `#include <stdio.h>` | `format`：输入的格式<br>...：输入的变量 | 输入字符数 | `EOF`       |
| `int fscanf(FILE *stream, const char *format, ...);` | `#include <stdio.h>` | `stream`：作为输入的流<br>`format`：输入的格式<br>...：输入的变量 | 输入字符数 | `EOF`       |
| `int sscanf(const char *str, const char *format, ...);` | `#include <stdio.h>` | `str`：作为输入的存储区<br>`format`：输入的格式<br>...：输入的变量 | 输入字符数 | `EOF`       |
| `int printf(const char *format, ...);` | `#include <stdio.h>` | `format`：输出的格式<br>...：输出的变量 | 输出字符数 | `EOF`       |
| `int fprintf(FILE *stream, const char *format, ...);` | `#include <stdio.h>` | `stream`：接收输出的流<br>`format`：输出的格式<br>...：输出的变量 | 输出字符数 | `EOF`       |
| `int sprintf(char *str, const char *format, ...);` | `#include <stdio.h>` | `str`：接收输出的存储区<br>`format`：输出的格式<br>...：输出的变量 | 输出字符数 | `EOF`       |

### 文件I/O

| 标志位         | 描述                                             |
|----------------|--------------------------------------------------|
| `O_RDONLY`     | 以只读的方式打开文件                               |
| `O_WRONLY`     | 以只写的方式打开文件                               |
| `O_RDWR`       | 以读写的方式打开文件                               |
| `O_APPEND`     | 以追加的方式打开文件                               |
| `O_ASYNC`      | 设置异步标志位，表示对设备文件操作采用信号通知的方式 |
| `O_CREAT`      | 如果文件不存在，自动创建                           |
| `O_EXCL`       | 如果文件存在，返回错误码                           |
| `O_NONBLOCK`   | 以非阻塞的方式打开文件，常用于管道操作               |
| `O_TRUNC`      | 如果文件存在，截取文件的长度为 0，即清空文件         |

| 函数原型                                                | 所需头文件                | 函数参数                                           | 返回值       |
|---------------------------------------------------------|-------------------------|----------------------------------------------------|--------------|
| `int open(const char *pathname, int flags);`             | `#include <sys/types.h>`<br>`#include <sys/stat.h>`<br>`#include <fcntl.h>` | `pathname`：要打开的文件路径<br>`flags`：打开文件的标志 | 非负文件描述符（成功）<br>`-1`（失败）  |
| `int open(const char *pathname, int flags, mode_t mode);` | `#include <sys/types.h>`<br>`#include <sys/stat.h>`<br>`#include <fcntl.h>` | `pathname`：要打开的文件路径<br>`flags`：打开文件的标志<br>`mode`：创建文件时的权限 | 非负文件描述符（成功）<br>`-1`（失败）  |
| `int close(int fd);`        | `#include <unistd.h>` | `fd`：文件描述符 | `0`（成功）<br>`-1`（失败） |
| `ssize_t write(int fd, const void *buf, size_t count);` | `#include <unistd.h>` | `fd`：文件描述符<br>`buf`：要写入的数据缓冲区<br>`count`：要写入的字节数 | 实际写入的字节数（成功）<br>`-1`（失败） |
| `ssize_t read(int fd, void *buf, size_t count);` | `#include <unistd.h>` | `fd`：文件描述符<br>`buf`：接收读取数据的缓冲区<br>`count`：要读取的字节数 | 实际读取的字节数（成功）<br>`0`（表示已到达文件末尾）<br>`-1`（失败） |
| `off_t lseek(int fd, off_t offset, int whence);` | `#include <sys/types.h>`<br>`#include <unistd.h>` | `fd`：文件描述符<br>`offset`：偏移量<br>`whence`：基准位置（`SEEK_SET`、`SEEK_CUR`、`SEEK_END`） | 新的文件偏移量（成功）<br>`-1`（失败） |
| `int fcntl(int fd, int cmd, ... /* arg */ );` | `#include <unistd.h>`<br>`#include <fcntl.h>` | `fd`：文件描述符<br>`cmd`：命令标志（操作类型）<br>`arg`：可选的参数（根据命令不同而变化） | 视不同命令而定 |