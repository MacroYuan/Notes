# C++面向对象

## 对象定义

C++对象体里定义属性和方法，在对象class体外才是方法的具体实现，这一点跟GO有点相似

```cpp
class className {
    public:
    // 成员函数声明
    private:
}
// 成员函数定义
```

C++对象是一个cpp文件，不像Java是一个class文件。

成员函数可以定义在类定义内部，或者单独使用**范围解析运算符::**来定义。在类定义中定义的成员函数是自动内联的。

创建对象

C++直接在声明对象名时就创建了一个对象，不像Java需要声明对象名后还要new一个实例才能创建对象。

```cpp
// c++
Human human;
human.sayHello();

// Java
Human human = new Human();
human.sayHello();
```

C++不像Java中每个属性和方法都能自定义访问修饰符，而是在类中通过public、private、protect将类定义分为三块，每块放该修饰的内容

```cpp
class Human {
    public:
    // 公有成员
    private:
    // 私有成员
    protected:
    // 受保护成员
}
```

protected成员在派生类（子类）中是可访问的，没有任何访问修饰符的成员是private（Java中是public）的



## 类构造函数 & 析构函数

C++的构造函数在每次创建类的新对象时执行，而不是new的时候。

C++中可以用初始化列表来初始化字段

```cpp
Human::Human(std::string name, std::string lastName, std::string gender, int age) : name(name), lastName(lastName), gender(gender), age(age) {
//    this->name = name;
//    this->lastName = lastName;
//    this->gender = gender;
//    this->age = age;
}
```

析构函数是一种特殊的成员函数，它会在每次删除所创建对象时执行。析构函数名与构造函数名相同，在前面多了个`~`符号，同样不会返回任何值，不带有任何参数。析构函数有助于在跳出程序（比如关闭文件、释放内存等）前释放资源。

```cpp
Human::~Human(void) {
    std::cout << "Object is being deleted" << std::endl;
}
```



## 拷贝构造函数

拷贝构造函数是一种特殊的构造函数，它在创建对象时，是使用同一类中之前创建的对象来初始化新创建的对象。常用于：

- 通过使用另一个同类型的对象来初始化新创建的对象。
- 复制对象把它作为参数传递给函数
- 复制对象，并从函数返回这个对象



## 继承

C++类继承格式

```cpp
class A {
};
class B : public A {
};
```

与Java中使用extends对象继承不同。C++的继承分为public、protected、private三种继承方式。

- public继承：父类public成员、protected成员、private成员的访问属性在子类中分别变成：public、protected、private；
- protected继承：父类public成员、protected成员、private成员的访问属性在子类中变成：protected、protected、private；
- private继承：父类public成员、protected成员、private成员的访问属性在子类中变成：private、private、private。

无论哪种继承方式，以下两点没有改变：

1. private成员只能被本类成员和友元访问，不能被子类访问；
2. protected成员可以被子类访问。