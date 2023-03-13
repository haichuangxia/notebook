# 文件读写函数
| 函数名   | 签名                                                                 | 用法               |
| -------- | -------------------------------------------------------------------- | ------------------ |
| fopen()  | `FILE *fopen(char *filename, char *mode);`                           | 用于打开一个文件   |
| fgetc()  | `int fgetc (FILE *fp);`                                              | 读取一个字符       |
| fputs()  | `int fputc ( int ch, FILE *fp );`                                    | 写入一个字符       |
| fgets()  | `char *fgets ( char *str, int n, FILE *fp );`                        | 按行读入一个字符串 |
| fputs()  | ` int fputs( char *str, FILE *fp );`                                 | 按行写入字符串     |
| fread()  | `size_t fread ( void *ptr, size_t size, size_t count, FILE *fp );`   | 读入字符块         |
| fwrite() | `size_t fwrite ( void * ptr, size_t size, size_t count, FILE *fp );` | 写入字符块         |
# 实例
``` c++
#include <stdio.h>
#include <stdlib.h>
#define N 100
int main() {
    FILE *fp;
    char str[N + 1];
    //判断文件是否打开失败
    if ( (fp = fopen("d:\\demo.txt", "rt")) == NULL ) {
        puts("Fail to open file!");
        exit(0);
    }
    //循环读取文件的每一行数据
    while( fgets(str, N, fp) != NULL ) {
        printf("%s", str);
    }

    //操作结束后关闭文件
    fclose(fp);
    return 0;
}
```

# 字符串处理函数
| 函数            | 签名                                                       | 作用                               |
| --------------- | ---------------------------------------------------------- | ---------------------------------- |
| insert()        | `string& insert (size_t pos, const string& str);`          | 在一个字符串的指定位置插入一个子串 |
| erase()         | `string& erase (size_t pos = 0, size_t len = npos);`       | 指定位置删除指定长度的字串         |
| substr()        | `string substr (size_t pos = 0, size_t len = npos) const;` | 从指定的位置提取指定长度的字串     |
| find()          |                                                            | 查找子串出现的位置                 |
| rfind()         |                                                            |
| find_first_of() |                                                            | 查找在字符串中首次出现的位置       |