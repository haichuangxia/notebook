# 复合数据类型

## union共用体

**union**(共用体):允许在**相同的内存区域**存储**不同类型**的数据类型,但是**任一**时刻**只能**有一个成员带值

### 定义union

定义的语法为:

``` c++
union [union tag]
{
   member definition;
   member definition;
   ...
   member definition;
} [one or more union variables];
```

`union`的大小为其成员中最大的成员的大小.

## struct结构体

