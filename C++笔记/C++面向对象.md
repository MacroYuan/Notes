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

```cpp
className(const className &obj) {
    // 构造函数的主体
}
```

```cpp
Human::Human(const Human &obj) {
    this->name = obj.name;
    this->lastName = obj.lastName;
    this->gender = obj.gender;
    this->age = obj.age;
}
```



## 友元函数

友元函数是定义在类外部，但是可以访问类的private成员和protected成员。通过关键字`friend`实现。

```cpp
class Human {
public:
    std::string name;
    std::string gender;
    int age;
    Human(std::string name, std::string lastName, std::string gender, int age);
    friend void sayHello(Human human);

private:
    std::string lastName;
};

void sayHello(Human human) {
    std::cout << human.lastName << std::endl;
}

Human::Human(std::string name, std::string lastName, std::string gender, int age) : name(name), lastName(lastName), gender(gender), age(age) {}


int main() {
    Human human("Macro", "Yuan", "Male", 24);
    sayHello(human);

    return 0;
}
```




## 内联函数

如果一个函数是内联的，在编译时，编译器会将调用该函数的地方替换为该函数体，而不是再去找函数的位置读取方法再使用。

在类定义中的定义的函数都是内联函数，即使没有使用 **inline** 说明符。



## 类指针

Java中的对象名相当于C++中的类指针的作用，指向了类实例

java

```java
Human human = new Human("Macro");
```

c++

```cpp
Human *human;
Human human1("Macro"); // 相当于new
human = &human1;
```



## 静态变量

类的成员被声明为静态时，意味着无论创建多少个类的对象，静态成员都只有一个副本。静态成员在类的所有对象中是共享的，这意味着human1，human2中的静态成员相同。

静态成员函数即使在类对象不存在的情况下也能被调用，静态函数只要使用类名+范围解析运算符::就可以访问。静态成员函数只能访问静态成员数据、其他静态成员函数和类外部的其他函数。

静态成员函数有一个类范围，他们不能访问类的 this 指针。您可以使用静态成员函数来判断类的某些对象是否已被创建。



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



C++中一个类可以继承多个类（java一个子类只能继承自一个父类）

多继承格式

```cpp
class <派生类名>:<继承方式1><基类名1>, <继承方式2><基类名2>, ... {
    <派生类类体>
};
```



## 重载

这部分与java相同，一个函数根据传入参数数量与格式不同有不同的实现。

运算符重载相当于一个语法糖的结构



## 多态

C++中没有interface，多态的实现是基于类继承的层级关系实现的。

```cpp
class Human{};
class Polic: public Human{};
class Doctor: public Human{};

int main() {
    Human *human;
    Police police;
    Doctor doctor;
    
    human = &police;
    human.sayHello();
    return 0;
}
```

sayHello()函数声明前需要放置`virtual`关键字，如果不适应`virtual`关键字，调用sayHello()函数会调用基类中的版本。



### 虚函数

虚函数是在基类中使用关键字`virtual`声明的函数。在子类中重写该方法，告诉编译器不要静态链接到该函数，而是根据指向内容动态选择调用函数。

### 纯虚函数

在基类中只给出函数定义，而不给实现（相当于接口方法）

```cpp
class Human {
    public:
    virtual void sayHello() = 0;
}
```

`= 0`告诉编译器，函数没有主体，上面的虚函数是纯虚函数。



## 接口

C++中的接口和抽象类一样，没有属性的抽象类（java中的接口），带属性的抽象类（java中可以多继承的抽象abstract类）



