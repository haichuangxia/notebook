# 基本运算符
| 类型     | 运算符 | 名称       | 意义                                             |
| -------- | ------ | ---------- | ------------------------------------------------ |
|          | *      | 指针运算符 | 获取一个地址上的数据                             |
|          |        |            | 定义指针变量(变量在内存中的地址叫做指针)         |
|          | &      | 寻址运算符 | 找一个变量的地址                                 |
|          | &      | 引用符     | 相当于起别名,用来传递聚合类型的数据(如对象,数组) |
|          | :      | 分隔符     |                                                  |
| 位运算符 | ^      | 按位异或   | 二进制按照位异或                                 |
|          | ~      | 取反       | 对二进制进行按位取反而不是对整数进行数值取反     |
|          | >> NUM | 逻辑右移   | 对无符号数进行逻辑移位操作,低位补0               |
|          | << NUM | 逻辑左移   | 对无符号数进行逻辑移位操作,低位补0               |


# 函数传参
## 基本数据类型传参
基本数据类型可以直接进行参数传递.
## 使用指针进行传参
## 引用传参
引用传参的方式比指针写起来更直观一些,更简介一些.
## 不同传参方式用途
``` C++
#include <iostream>
using namespace std;
void swap1(int a, int b);
void swap2(int *p1, int *p2);
void swap3(int &r1, int &r2);
int main() {
    int num1, num2;
    cout << "Input two integers: ";
    cin >> num1 >> num2;
    swap1(num1, num2);
    cout << num1 << " " << num2 << endl;
    cout << "Input two integers: ";
    cin >> num1 >> num2;
    swap2(&num1, &num2);
    cout << num1 << " " << num2 << endl;
    cout << "Input two integers: ";
    cin >> num1 >> num2;
    swap3(num1, num2);
    cout << num1 << " " << num2 << endl;
    return 0;
}
//直接传递参数内容
void swap1(int a, int b) {
    int temp = a;
    a = b;
    b = temp;
}
//传递指针
void swap2(int *p1, int *p2) {
    int temp = *p1;
    *p1 = *p2;
    *p2 = temp;
}
//按引用传参
void swap3(int &r1, int &r2) {
    int temp = r1;
    r1 = r2;
    r2 = temp;
}
```

# 条件判断
## 条件运算符
是一个三目运算符,其语法为:`表达式1 ? 表达式2 : 表达式3`,`:`用于分开两个表达式,其语意为:
``` C
if(表达式1) 执行表达式2;
else 执行表达式3;
```

注意:条件表达式可以嵌套使用

### 常见用法
用于根据表达式进行赋值

``` c
max = (a>b) ? a : b;
```

等同于:
``` c
if(a>b){
    max = a;
}else{
    max = b;
}
```

## switch case
default是可选的
### 不使用break的case语句
单纯使用case语句,及时条件达到后也将继续执行后面的case语句.
``` c
int a=0;
switch(a){
    case 0: printf("0");
    case 1: printf("1");
    case 2: printf("2");
    default:printf("default")
}
// out:0 1 2 default
```

### 使用cbreak的case语句

使用`break`关键字跳出后面的case
``` c
int a=0;
switch(a){
    case 0: printf("0");break;
    case 1: printf("1");break;
    case 2: printf("2");break;
    default:printf("default")
}
// out:0
```