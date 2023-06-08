



# warning
##  pointer of type ‘void *’ used in arithmetic

在使用指针进行算数运算时候，有时候会出现这个问题，如：

``` c
// 想要将ps的16-31字节复制到pd中去
void *pd=malloc(16);
void *ps=malloc(32);
memcpy(pd,ps+16,16);
/**
warning:pointer of type "void*" used in arithmetic
*/
```

原因：使用指针进行计算时获取第几个元素的地址时，需要两个参数，一个是元素类型，一个是元素索引。void类型的指针，没有元素类型，因此很多时候没办法算第i个元素的地址。



改正：

```c
memcpy(pd,(uint8_t*)ps+16,16);
```

reference:[消除 pointer of type ‘void *’ used in arithmetic告警_EINPROGRESS的博客-CSDN博客](https://blog.csdn.net/kwanson/article/details/80962370)
