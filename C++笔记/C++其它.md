## 异常处理

和java一样，C++中也是使用try、catch、throw捕捉异常并处理，不同的是throw抛出的不止是异常，可以是任意表达式，相应的catch块也要捕捉相应的表达式结果。



## 动态内存

C++中的动态内存基于堆和栈实现。

- `栈`：在函数内部声明的所有变量将占用栈内存。
- `堆`：程序中未使用的内存，在程序运行时可用于动态分配内存。

C++中使用new动态分配内存

```cpp
double* point = NULL; // 初始化为NULL的指针
point = new double; // 为变量请求内存
```

当new返回NULL指针表示存储区用完，无法分配内存。

使用delete关键字将申请的内存释放

```cpp
delete point; // 释放point所指向的内存
```

数组动态分配

```cpp
char *point = NULL;
point = new char[20];  // 可直接写成 char *point = new char[20];

delete [] point; // 删除point所指向的数组
```

对象动态分配

```cpp
Human *human = new Human("Macro");

delete human;
```



## 命名空间

Java通过使用双亲委派机制实现类加载的不冲突，而在C++中通过使用**命名空间**解决。

定义命名空间

```cpp
namespace namespaceName {
    // 代码声明
}
```

调用带有命名空间的函数

```cpp
namespaceName::code; // code可以是变量或函数
```

```cpp
namespace namespace1 {
    void foo() {
        std::cout << "namespace1" << std::endl;
    }
}

namespace namespace2 {
    void foo() {
        std::cout << "namespace2" << std::endl;
    }
}

int main() {
    namespace1::foo();
    namespace2::foo();

    return 0;
}
```

通过使用`using`指令，可以在使用命名空间时不用在前面加上命名空间的名称。



## 预处理器

`#include`用于把头文件包含到源文件中；

`#define`用于创建符号常量

```cpp
#define PI 3.1415826
#define MIN(a,b) (a<b ? a : b)
```

条件编译

```cpp
#ifdef NULL
	#define NULL 0
#endif
```

