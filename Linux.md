伪随机数获取

 你可以使用以下命令从`/dev/urandom` 获得 `1600` 字节的伪随机数

``` 
head -c  1600 /dev/urandom | hexdump
```

 | 是管道符，pipe到hexdump中，使用hexdump来查看二进制文件的十六进制编码

推荐使用/dev/urandom获取随机数

```c
#define LEN 16 // 128 bits
unsigned char *key = (unsigned char *) malloc(sizeof(unsigned char)*LEN);
FILE* random = fopen("/dev/urandom", "r");
fread(key, sizeof(unsigned char)*LEN, 1, random);
fclose(random);
```

