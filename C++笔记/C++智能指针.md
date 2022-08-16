# C++智能指针

像Java、Go的垃圾 回收装置，C++中通过智能指针进行智能内存的收回。

常见的两种智能指针是，**unique_ptr**和**shared_ptr**。

### unique_ptr

unique_ptr是最简单、最容易使用的一个智能指针，在声明时必须使用模板参数指定类型：

```cpp
unique_ptr<int> ptr1(new int(10));    // int智能指针
assert(*ptr1 == 10);
assert(ptr1 != nullptr);


unique_ptr<string> ptr2(new string("hello"));    // string智能指针
assert(*ptr2 == "hello");
assert(ptr2->size() == 5);
```

unique_ptr相当于一个对象，不能使用delete释放，自动管理初始化的指针，在离开作用域时析构释放内存。

该指针不能随意移动地址，不能执行加减运算。

在Java中的创建实例`Object object = new Object()`等价于`unique_ptr<Object> object(new Object())`

使用工厂函数 **make_unique()** 创建指针时强制初始化，同时还可以自动类型推导auto。

make_unique()需要C++14版本，C++11需要自己实现，如下：
```cpp

template<class T, class... Args>

std::unique_ptr<T>

my_make_unique(Args&&... args)

{

    return std::unique_ptr<T>(

        new T(std::forward<Args>(args)...));

}

```

### unique_ptr的所有权

unique_ptr不允许共享，任何时候只能有一个人持有它。unique_ptr禁止拷贝复制，必须使用**std::move()**函数显式地声明所有权转移。

指针的所有权被转移后，原来的unique_ptr就变为了空指针，新的unique_ptr接替了管理权：

```cpp
auto ptr1 = make_unique<int>(42);
assert(ptr1 && *ptr1 == 42);


auto ptr2 = std::move(ptr1);
assert(!ptr1 && ptr2);
```

### shared_ptr

shared_ptr的所有权是可以被共享的，支持拷贝赋值，允许多个人同时持有。shared_ptr内部使用了**引用计数**。引用计数为几表示有几个人持有该智能指针，如果发生拷贝赋值——也就是共享的时候，引用计数就增加，而发生析构销毁的时候，引用计数就减少。只有当引用计数减少到 0，也就是说，没有任何人使用这个指针的时候，它才会真正调用 delete 释放内存。



### weak_ptr

weak_ptr为打破shared_ptr的循环引用设计，weak_ptr不会增加引用计数（弱引用），不一定要持有对象，但在需要的时候，可以调用成员函数lock()，获取到shared_ptr(强引用)。

```cpp
auto ptr1 = make_shared<int>(64);


weak_ptr<int> ptr2 = ptr1;


auto sp = ptr2.lock(); // 升级为强引用
auto sp1 = ptr2.promote(); // 同样升级为强引用
```
