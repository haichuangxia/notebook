C++常用的符号说明:

# `::`作用域运算符
## 局部变量与全局变量同名时,引用全局变量
`::x`表示使用全局变量,`x`表示局部变量:
``` C++
x=0;
void test(){
    x=1;
    cout <<::x <<end1;
    cout <<x <<end1;

    return 0;
}
```
# REFERENCE
1. [c++入门学习篇（1）之：：作用域符解析](https://zhuanlan.zhihu.com/p/137383328)