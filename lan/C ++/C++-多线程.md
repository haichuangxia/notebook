``` c++
#include <iostream>
#include <thread>
using namespace std;
void thread_task()
{
    cout << "thread son\n" << endl;
}

int main()
{
    thread t(thread_task);
    cout << "thread main\n" << endl;
    t.join();
    return 0;
}
/** out
thread main
thread son
*/
```

原因：第11行启动了子线程，但是启动子线程并执行函数需要一定的时间，而这个时间在这个环境下大于执行主线程的第12行的打印语句的时间，因此会出现明明11行先注册了子线程，但是12行的父线的语句先执行的情况。

现在交换第12行和13行，使用`join()`函数：

``` c++
#include <iostream>
#include <thread>
using namespace std;
void thread_task()
{
    cout << "thread son\n" << endl;
}

int main()
{
    thread t(thread_task);
    t.join();
    cout << "thread main\n" << endl;
    return 0;
}
/**
thread son

thread main
*/
```

`join()`:只有执行完子线程的函数之后才会继续执行`join()`之后的代码，在子线程执行期间，主线程出于阻塞状态。`jion()` 执行的时候会阻塞父线程，等子线程执行完毕的之后才会解除父线程的阻塞状态。

# 性能分析

影响c++性能库的主要因素在于多个线程对互斥资源的竞争。




# 线程的同步与互斥

``` c++
#include <iostream>
#include <thread>
#include <string>
using namespace std;

void thread_task()
{
    for (int i = 0; i < 10; i++)
    {
        cout << "print thread: " << i << endl;
    }
}
int main()
{
    thread t(thread_task);
    for (int i = 0; i > -10; i--)
    {
        cout << "print main: " << i << endl;
    }
    t.join();
    return 0;
}
```

在此例中，结果不可预测，因此` cout << "print main: " << i << endl;`语句的执行并不是原子性的，不是线程安全的，因此程序的打印结果和时间片有关。一个可能的输出结果是：`print main: print thread: 0`,即不能完整的打印一个语句再打印下一个语句。 

## 使用mutex实现进程的互斥

### 写法一：手动lock与unlock

``` c++
#include <iostream>
#include <thread>
#include <string>
#include <mutex>
using namespace std;
 
mutex mt;
void thread_task()
{
    for (int i = 0; i < 10; i++)
    {
        mt.lock();
        cout << "print thread: " << i << endl;
        mt.unlock();
    }
}
 
int main()
{
    thread t(thread_task);
    for (int i = 0; i > -10; i--)
    {
        mt.lock();
        cout << "print main: " << i << endl;
        mt.unlock();
    }
    t.join();
    return 0;
}
```

在此例中，声明一个全局的互斥量，然后使用`lock()`和`unlock()`实现资源的上锁与解锁，实现互斥（标准输入输出也是一个资源）

## 原子操作

使用mutex实现互斥访问的一个缺点是性能较大的损失。如果既想使用互斥访问来保证程序运行结果的正确，又想尽量提高程序的性能，此时就可以使用**原子操作**(使用mutex互斥量其实也是一种原子操作)。C++11开始从语言层支持并行编程，提供包括原子操作，线程同步，线程管理，共享数据保护等各种功能。定义原子操作需要引入头文件`atomic`.

``` c++
#include <iostream>
#include <thread>
#include <atomic>
#include <time.h>
#include <mutex>
using namespace std;
 
#define MAX 100000
#define THREAD_COUNT 20
 
//原子操作
atomic_int total(0);
 
void thread_task()
{
    for (int i = 0; i < MAX; i++)
    {
        total += 1;
        total -= 1;
    }
}
 
int main()
{
    clock_t start = clock();
    thread t[THREAD_COUNT];
    for (int i = 0; i < THREAD_COUNT; ++i)
    {
        t[i] = thread(thread_task);
    }
    for (int i = 0; i < THREAD_COUNT; ++i)
    {
        t[i].join();
    }
    
    clock_t finish = clock();
    // 输出结果
    cout << "result:" << total << endl;
    cout << "duration:" << finish - start << "ms" << endl;
 
    return 0;
}
```

在此例中，第12行定义了一个原子整数类型 `total`并初始化赋值为`0`.

定义原子类型的变量：注意：`atomic<T>`不一定有锁也不一定无锁，和平台有关。

| 变量类型           | 方式                            |
| ------------------ | ------------------------------- |
| 内置的基本数据类型 | atomic_DATATYPE,如atomic_uint64 |
| 自定义的类         | atomic<Typename>                |

### 使用条件变量实现进程同步

通过使用条件变量，可以是的线程进入休眠，并且主动唤醒一个线程，而不是让线程一直通过循环来判断是否可以开始执行。（主动查询和被动唤醒）

- wait():阻塞一个线程
- notify_one():唤醒一个线程

使用条件变量的一般模型：生产者和消费者

- 消费者循环判断条件变量的值，如果不符合条件则持续wait
- 生产者生产之后通过notify_one唤醒一个消费者

## 线程管理

| 功能         | 解释                                       | 函数           | 注意                                                         |
| ------------ | ------------------------------------------ | -------------- | ------------------------------------------------------------ |
| 分离线程     | 会让线程在后台运行，主线程不能与之直接交互 | `detach()`函数 |                                                              |
| 等待线程完成 |                                            | `join()`       | 在没有异常时使用join时，需要在异常处理过程中调用join，避免生命周期问题 |

分离线程的案例：使用vscode打开多个窗口，每个窗口各自编辑一个文档。虽然每个窗口都有独立的选项，看起来完全独立，但是他们运行在同一个应用实例中。因此，一种方式是为每个文档窗口启动一个线程，每个线程完全分离。



# 事件驱动编程

使用事件驱动编程的基本原理：os的pv操作，通过等待和唤醒机制来实现使用事件驱动。
