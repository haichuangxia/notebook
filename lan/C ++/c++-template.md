模板:是泛型编程的基础.。



**模板函数的定义和实现都需要在h头文件中，不能在cpp文件中实现**

# 函数模板

模板函数定义的一般形式为:

```c++
template <typename type>
ret-type func-name(parameter list)
{
   // 函数的主体
}
```

例如定义:

``` c++
template <typename T> 
inline T const& Max (T const& a, T const& b) 
{ 
    return a < b ? b:a; 
} 
```

# 类模板

泛型类声明的方式为:

``` c++
template <class type> 
class class-name {
.
.
.
}
```

# 泛型

C++中泛型以模板为基础.泛型基于**类型的参数化**,**，数据的类型也可以通过参数来传递，在函数定义时可以不指明具体的数据类型，当发生函数调用时，编译器可以根据传入的实参自动推断数据类型。这就是类型的参数化。**

泛型函数的定义:
``` c++
template<typename T> void Swap(T *a, T *b){
    T temp = *a;
    *a = *b;
    *b = temp;
}
```
`template`:定义函数模板的关键字
`<typename T>`:类型参数,或者说类型占位符.`typename`:指明具体类型参数的关键字
`T`:一个类型参数
`template<typename T>`:模板头

## typename关键字的使用

`typename`是c++中泛型编程时的一个关键字,相当于`class`的作用.用来表示一个泛型声明中的非独立名称是一个**类型**,而不是一个**变量**(如`int`是类型,但是`int a`就是变量).typename 的作用就是告诉 c++ 编译器，typename 后面的字符串为一个类型名称，而不是成员函数或者成员变量.

``` c++
// 表示一个类型
class A
{
public:
    typedef int a_type;
    int a;
};

// 表示一个变量
class B
{
    B() {}

public:
    static int a_type;
};

int B::a_type = 1;

template <typename param, typename value>
class C
{
public:
    typedef A::a_type type1; // 正确，正常使用A的a_type类[没有typename则默认表示是一个变量]
    typedef typename param::a_type type2;
    // 必须要在param 前面加上typename，向编译器说明这个模块类的a_type指的是一种类型

    C()
    {
        c = value::a_type; // 模板形参的静态成员变量
    }

    int c;
};

int main()
{

    C<A, B> c;

    return 0;
}
```

即:如果在一个类中,`typename`后面的类型与模板形参相关时,就需要使用`typename`表示这是一个类型.如在此例中,第*25行*中的`param::a_type`,`param`是模板形参,.`a_type`是在第*5行*中使用`typedef`关键字定义的类型,而模板类c的模板形参为:`template <typename param,typename value>`

`typedef typename PointCloudSource::Ptr PointCloudSourcePtr;`这种用法可以断句为:`typedef (typename PointCloudSource::Ptr) PointCloudSourcePtr;`,其中`typename PointCloudSource::Ptr`表示`PointCloudSource::Ptr`是一个类型(一般是用`typedef`定义的类型别名),而不是一个变量.

# REFERENCE

1. [C++中的typedef typename](https://zhuanlan.zhihu.com/p/106198289)
2. [C++中typename的用法](https://cloud.tencent.com/developer/article/1805454)