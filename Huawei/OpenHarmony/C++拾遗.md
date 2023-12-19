## const用法

const用于变量，表示该变量值无法被更改，用于定义常量

const用于函数返回值和形参，表示该参数和函数返回值无法实现替换更改

const用于类成员函数，表示在该函数受到强制保护，内部无法更改到函数外部的值

如果一个类方法设计类成员变量的更改，该方法使用const修饰后，内部修改将不生效

```cpp
class Test {
public:
    void function() const {
        i = 11;
    }
private:
    int i = 0;
}

int main(){
    Test test;
    test.function();
    cout << test.i << endl; // i的值仍未0
}
```

```cpp
class Test {
public:
    std::function<void()> functionA() {
        int i = 11;
        auto task = [this, i] (){
            this.functionB(i);
        }
    }
private:
    void functionB(int i) {
        cout << i << endl;
    }
}

int main() {
    Test test;
    auto task = test.functionA();
    task(); // 输出i的值未知
}
```



## 格式化日志

```cpp
%d   //输出十进制
%s   //输出char*字符串
%zu  //输出size_t，size_t在32位和64位系统中格式化不同
%ld  //输出long类型，32位系统为32位，64位系统为64位
%lld //输出long long类型，32位与64位系统都是64位
%p   //输出指针指向的地址
```

- 黄蓝同步问题

  有时蓝区编译过门禁成功了，黄区门禁失败了，大多是打印日志格式问题，为了避免黄蓝同步问题，一定要注意32位和64位的格式化日志

  - 打印size时用%zu
  - 打印long类型变量时，将变量进行下强制转化`static_cast<long long>`，并配合%lld打印
  - 打印long long类型变量或int64_t类型变量，使用%lld打印



## static用法

