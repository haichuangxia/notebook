# 创建字符串

```c++
 unsigned char *key = (unsigned char *)"01234567890123456789012345678901";
```

TODO:

- [ ] 使用数组和指定创建字符串有什么区别

## 字符串的输出

使用`printf()`或者`std::cout`进行字符串的输出打印时,需要传入一个内存地址。**字符串打印从内存起始地址开始，以读到字符串结束标志`\0`为止**。

以下例子说明这一点:

```c++
 char ctext[][16]={"0123456789abcde","fedcba987654321"};
 char key[]="0123456789abcdef";
 memcpy((void *)ctext,(void *)key,16);
 printf("%s\n",ctext[0]);
 printf("%d\n",sizeof(ctext[0]));
/** output
the contents of ctext[0] is: 0123456789abcdeffedcba987654321
the length of ctext[0] is : 16
*/
```





