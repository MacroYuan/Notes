C++内存管理

C++没有像Java那样的垃圾回收装置自动对对象进行管理，在对象使用完毕后，需要通过delete()将对象删除，否则会造成内存泄露的问题。



C++指针

在C++中使用`*`表示指针，使用`&`修饰地址

- `&b`取b的值地址，它被定义为**取地址运算符**
- `*a`取a的值，它被定义为**间接寻址运算符**，返回操作数所指定地址的变量的值



C++指针与引用

指针传递

```cpp
void add(int *a) {}
int b = 3;
add(&b);
```

引用传递

```cpp
void add(int &a) {}
int b = 3;
add(b);
```

指针和引用区别

- 引用被创建时必须被初始化

- 不能有NULL引用

- 一旦引用被初始化，就不能改变引用关系

指针传递本质上是值传递的方式，它所传递的是一个地址值



C++内存管理

栈

```cpp
void bat(int n) {int a = n + 1;}
class Obj {
    public:
    Obj() {}
    ~Obj() {}
};
void foo(int n) {
    Obj obj;
}
```

带有`~`符号的函数是析构函数，对象在释放内存时会调用对应的析构函数，哪怕发生了异常退出，C++内存管理都会执行对象的析构函数来释放。

堆

在C++中，也有和Java一样的属于动态分配的区域，靠new关键字来申请空间，但不同的是C++中需要显式的delete，用于释放分配区域，而Java则是通过GC回收。所以在C++中，new和delete要成对出现。考虑问题：new了一个对象，在还没来得及delete时程序奔溃，则该对象一直占用内存。C++提供了智能指针等可以优雅地释放内存。

```c++
auto ptr = new std::vector();
delete ptr;
```



RALL

RALL(Resource Acquisition Is Initialzation，资源获取即初始化)，RALL要求：资源的有效期与持有资源的对象的生命期严格绑定，即由对象的构造函数完成资源的分配（获取），同时由析构函数完成资源的释放。在这种要求下，只要对象能正确地析构，就不会出现资源泄露的问题。

```cpp
class TestRALL {
    public:
    TestRALL() {
        std::cout << "TestRALL done" << std::endl;
    };
    
    ~TestRALL() {
        std::cout << "~TestRALL done" << std::endl;
    };
    
    void print() {
        std::cout << 1 << std::endl;
    }
};

TestRALL *createTest() {
    return new TestRALL();
}

void print() {
    auto ta = createTest();
    ta -> print();
}

int main() {
    print();
    return 0;
}
```

以上代码并没有调用析构函数，TestRALL对象一直存在。

```cpp
class TestRALL {
    public:
    TestRALL() {
        std::cout << "TestRALL done" << std::endl;
    };
    
    ~TestRALL() {
        std::cout << "~TestRALL done" << std::endl;
    };
    
    void print() {
        std::cout << 1 << std::endl;
    }
};

TestRALL *createTest() {
    return new TestRALL();
}

void print() {
    auto ta = createTest();
    ta -> print();
    // 新加
    delete ta;
}

int main() {
    print();
    return 0;
}
```

这里的delete就调用了析构函数