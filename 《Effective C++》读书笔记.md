## 1 视C++为一个语言联邦

- C

- Object-Oriented C++

- Template C++

- STL

## 2 尽量以const、enum、inline替换#define

## 3 尽可能使用const

## 4 确定对象被使用前已先被初始化

## 5 了解C++ 默默编写并调用哪些函数

即使是空类在构建时，也会默认添加上构造函数、析构函数、copy函数、copy assignment操作符

## 6 若不想使用编译器自动生成的函数，就应该明确拒绝

## 7 为多态基类声明virtual析构函数

virtual函数的目的是允许derived class的实现得以客制化

## 8 别让异常逃离析构函数

## 9 绝不在构造和析构过程中调用virtual函数

## 10 令operator= 返回一个referance to *this

## 11 在operator= 中处理“自我赋值”

## 12 复制对象时勿忘其每一个成分

# 三、资源管理

## 13 以对象管理资源

## 14 在资源管理中心小心copying行为

## 15 在资源管理类中提供对原始资源的访问

## 16 成对使用new和delete时要采取相同形式

## 17 以独立语句将newed对象置入智能指针

# 四、设计与声明

## 18 让接口容易被正确使用，不易被误用

## 19 设计class犹如设计type

## 20 宁以pass-by-reference-to-const替换pass-by-value

## 21 必须返回对象时，别妄想返回其reference

```cpp
inline const Rational operator* (const Rationcal &lhs, const Rational &rhs)
{
    return Rational(lhs.n * rhs.n, lhs.d * rhs.d);
}
```

## 22 将成员变量声明为private

## 23 宁以non-member、non-friend替换member函数

## 24 若所有参数皆需类型转换，请以此采用non-member函数

## 25 考虑写出一个不抛异常的swap函数

# 实现

## 26 尽可能延后变量定义式的出现时间

## 27 尽量少做转型动作

## 28 避免返回handles指向对象内部成分

## 29 为“异常安全”而努力是值得的

## 30 透彻了解inline的里里外外

## 31 将文件间的 编译依存关系降至最低

# 六、继承与面向对象设计

## 32 确定你的public继承塑摸出is-a关系

## 33 避免遮掩继承而来的名称
