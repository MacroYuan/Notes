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

## 34 区分接口继承和实现继承

## 35 考虑virtual函数以外的其他选择

## 36 绝不重定义继承而来的non-virtual函数

## 37 绝不重新定义继承而来的缺省参数值

## 38 通过复合塑模出has-a或”根据某物实现出“

## 39 明智而审慎地使用private继承

## 40 明智而审慎地使用多重继承

# 七、模板与泛型编程

## 41 了解隐式接口和编译期多态

## 42 了解typename的双重意义

## 43 学习处理模板化基类内的名称

## 44 将与参数无关的代码抽离templates

## 45 运用成员函数模板接受所有兼容类型

## 46 需要类型转换时请为模板定义非成员函数

## 47 请使用traits classes表现类型信息

## 48 认识template元编程

# 八、定制new和delete

## 49 了解new-handler的行为

## 50 了解new和delete的合理替换时机

## 51 编写new和delete时需要固守常规

## 52 写了placement new也要写placement delete

# 九、杂项讨论
