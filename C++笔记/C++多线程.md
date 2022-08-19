# C++ 多线程

在C++中，线程就是一个独立运行的函数，比如一个lambda表达式

```cpp
auto f = []()
{
    cout << "tid=" <<
        this_thread::get_id() << endl;
};


thread t(f);
```

多线程考虑问题：同步、死锁、数据竞争、系统调度开销等。

## 多线程开发

- 仅调用一次

- 线程局部存储

- 原子变量

- 线程对象

### 仅调用一次

使用**once_flag**类型的变量

```cpp
static std::once_flag flag;  // 全局的初始化标志
```

然后调用**call_once()**函数，以函数编程的方式，传递这个标志和初始化函数。这样C++会保证多个线程重入call_once()，也只有一个线程会成功运行初始化。

```cpp
auto f = []()
{
    std::call_once(flag,
        [](){
            cout << "only once" << endl;
        }
    );
};

thread t1(f);
thread t2(f);
```

### 线程局部存储

在 C++ 里由关键字 **thread_local** 实现**线程局部存储**，有 thread_local 标记的变量在每个线程里都会有一个独立的副本，是“线程独占”的，所以就不会有竞争读写的问题。

```cpp
thread_local int n = 0;    // 线程局部存储变量

auto f = [&](int x)
{
    n += x;
    cout << n;
};


thread t1(f, 10);
thread t2(f, 20);
// 结果为10和20，互不影响
```

### 原子变量

原子化(atomic)操作，意思是不可分的，操作要么完成，要么未完成，不能被任何外部操作打断，总有一个确定的、完整的状态。所以也不会存在竞争读写的问题，不需要使用互斥量来同步，成本低。

目前C++只能让一些基本类型原子化

```cpp
using atomic_bool = std::atomic<bool>;    // 原子化的bool
using atomic_int  = std::atomic<int>;      // 原子化的int
using atomic_long = std::atomic<long>;    // 原子化的long
```

原子操作包括store、load、fetch_add、fetch_sub、exchange、compare_weak/compare_exchange_strong。

### 线程对象

C++中使用专门的线程类thread，简单创建线程，在std::this_thread中，还有yield()、get_id()、sleep_for()、sleep_until() 等几个方便的管理函数。

async()，是**异步执行**一个任务，隐含的动作是启动一个线程去执行，但不保证立即启动。

async()会返回一个future变量，相当于一个结果存放器，如果任务有返回值，就可以用成员函数get()获取。注意：get()只能调用一次，再次获取结果会发生错误，抛出异常std::future_error。

注意：如果不显式获取async()的返回值，它就会同步阻塞直至任务完成，于是async就变为sync。

### mutex

**std::mutex**作为C++中的互斥量，只能由一个对象获取，不支持递归地对std::mutex对象上锁，而std::recursive_lock则可以。

### lock_guard

std::lock_gurd是C++11中定义的模板类。

lock_guard对象通常用于管理某个锁（Lock）对象，在某个lock——guard对象的声明周期内，它所管理的锁对象会一直保持上锁状态；而lock_guard的生命周期结束后，它所管理的锁对象才会被解锁。

### condition_variable

条件变量Condtion Variable是在多线程程序中用来实现**等待->唤醒 逻辑常用的方法**
