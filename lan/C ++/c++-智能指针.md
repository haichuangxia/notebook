c/c++中,借助指针,可以直接对内存进行操作.但是这也需要**手动对内存区进行管理**.传统c++种,创建对象使用`new`和`delete`去申请和释放内存.但是c++11引入了智能指针的概念,使用了**引用计数**的方法,不再需要程序员手动释放内存.

这些智能指针包含于`memory`头文件中,包括:

1. std::shared_ptr
2. std::unique_ptr
3. std::weak_ptr

注意:引用计数不是垃圾回收,

# 智能指针基本介绍

智能指针:在`<memory>`头文件中的`std`命名空间中定义.

内存泄漏:申请了内存,但是没有释放,导致内存空间白白浪费.

`unique_ptr`是`c++11`提供的,*用于防止内存泄漏*的**智能指针**的一种是实现.

# std::unique_ptr

`std::unique_ptr` 是一种独占的智能指针，它禁止其他智能指针与其共享同一个对象，从而保证代码的安全.即无法通过赋值的方式,让其他指针与其共享同一个对象.独占:即该指针无法被复制,但是可以使用`std::move`将其转移给其他的`unique_ptr`

``` c++
std::unique_ptr<int> pointer = std::make_unique<int>(10); // make_unique 从 C++14 引入
std::unique_ptr<int> pointer2 = pointer; // 非法
```

## 初始化智能指针

1. 指针直接初始化
2. 通过`move`构造,但不能通过拷贝构造
3. 通过`make_unique`构造


# REFERENCE
1. http://c.biancheng.net/view/7909.html