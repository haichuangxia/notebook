STL的基础知识,只是一个纲要.



| STL的组成  | 含义                                                         |
| ---------- | ------------------------------------------------------------ |
| 容器       | 一些封装[数据结构](http://c.biancheng.net/data_structure/)的模板类，例如 vector 向量容器、list 列表容器等。 |
| 算法       | STL 提供了非常多（大约 100 个）的数据结构算法，它们都被设计成一个个的模板函数，这些算法在 std 命名空间中定义，其中大部分算法都包含在头文件 <algorithm> 中，少部分位于头文件 <numeric> 中。 |
| 迭代器     | 在 [C++](http://c.biancheng.net/cplus/) STL 中，对容器中数据的读和写，是通过迭代器完成的，扮演着容器和算法之间的胶合剂。 |
| 函数对象   | 如果一个类将 () 运算符重载为成员函数，这个类就称为函数对象类，这个类的对象就是函数对象（又称仿函数）。 |
| 适配器     | 可以使一个类的接口（模板的参数）适配成用户指定的形式，从而让原本不能在一起工作的两个类工作在一起。值得一提的是，容器、迭代器和函数都有适配器。 |
| 内存分配器 | 为容器类模板提供自定义的内存申请和释放功能，由于往往只有高级用户才有改变内存分配策略的需求，因此内存分配器对于一般用户来说，并不常用。 |

这些组成成分,包含在下列头文件中:

| <iterator> | <functional> | <vector>  | <deque>  |
| ---------- | ------------ | --------- | -------- |
| <list>     | <queue>      | <stack>   | <set>    |
| <map>      | <algorithm>  | <numeric> | <memory> |
| <utility>  |              |           |          |

# 迭代器

## 迭代器类型

STL标准库为每种标准容器定义了一种迭代器类型.不同容器的迭代器有所不同,其功能强弱也不同.

| 迭代器类别                               | 特点                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| 前向迭代器(forward iterator)             | 假设 p 是一个前向迭代器，则 p 支持 ++p，p++，*p 操作，还可以被复制或赋值，可以用 == 和 != 运算符进行比较。此外，两个正向迭代器可以互相赋值 |
| 双向迭代器（bidirectional iterator）     | 双向迭代器具有正向迭代器的全部功能，除此之外，假设 p 是一个双向迭代器，则还可以进行 --p 或者 p-- 操作（即一次向后移动一个位置）。 |
| 随机访问迭代器（random access iterator） | 随机访问迭代器具有双向迭代器的全部功能。除此之外，假设 p 是一个随机访问迭代器，i 是一个整型变量或常量，则 p 还支持以下操作：<br />1 p+=i：使得 p 往后移动 i 个元素。<br />2 p-=i：使得 p 往前移动 i 个元素。<br />3 p+i：返回 p 后面第 i 个元素的迭代器。<br />4 p-i：返回 p 前面第 i 个元素的迭代器。<br />5 p[i]：返回 p 后面第 i 个元素的引用。 |

不同容器所使用的迭代器类型为:

| 容器                               | 对应的迭代器类型 |
| ---------------------------------- | ---------------- |
| array                              | 随机访问迭代器   |
| vector                             | 随机访问迭代器   |
| deque                              | 随机访问迭代器   |
| list                               | 双向迭代器       |
| set / multiset                     | 双向迭代器       |
| map / multimap                     | 双向迭代器       |
| forward_list                       | 前向迭代器       |
| unordered_map / unordered_multimap | 前向迭代器       |
| unordered_set / unordered_multiset | 前向迭代器       |
| stack                              | 不支持迭代器     |
| queue                              | 不支持迭代器     |

## 迭代器定义方式

不同容器具有不不同类别的迭代器,但是不同类的迭代器定义方式较为统一,有以下四种:

| 迭代器定义方式 | 具体格式                                   |
| -------------- | ------------------------------------------ |
| 正向迭代器     | 容器类名::iterator 迭代器名;               |
| 常量正向迭代器 | 容器类名::const_iterator 迭代器名;         |
| 反向迭代器     | 容器类名::reverse_iterator 迭代器名;       |
| 常量反向迭代器 | 容器类名::const_reverse_iterator 迭代器名; |

定义的迭代器实质是一个指针,需要使用取数运算符*读取迭代器指向的元素.

不同的定义方式含义为:

| 方式   | 特点                                               |
| ------ | -------------------------------------------------- |
| 正向   | 进行 ++ 操作时，迭代器会指向容器中的**后一个元素** |
| 反向   | 进行 ++ 操作时，迭代器会指向容器中的**前一个元素** |
| 常量   | 不能修改迭代器所指向的元素                         |
| 非常量 | 还能修改其指向的元素                               |

用于容器的遍历