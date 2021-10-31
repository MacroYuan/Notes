# 第二章 Java基础

![img](https://i.redd.it/rvj3mls30gy61.jpg)

## 2.3 反射

### 2.3.2 反射机制

反射机制指在程序运行过程中，对任意一个类都能获取其所有属性和方法，并且对任意一个对象都能调用其任意一个方法。这种动态获取类和对象的信息，以及动态调用对象的方法的功能被称为Java语言的反射机制。

### 2.3.3 反射的应用

Java中的对象有两种类型：编译时类型和运行时类型。编译时类型指在声明对象时所采用的类型，运行时类型指为对象赋值时所采用的类型。

在如下代码中，person对象的编译时类型为Person，运行时类型为Student，因此无法在编译时获取Student类中定义的方法：

```java
Person person = new Student();
```

因此，程序在编译期间无法预知该对象的信息和类的真实信息，只能通过运行时信息来发现该对象和类的真实信息，而真实信息（对象的属性和方法）通常通过反射机制来获取，这便是Java语言中反射机制的核心功能。

### 2.3.4 Java反射API

- Class类：用于获取类的属性、方法等信息。
- Field类：表示类的成员变量，用于获取和设置类中的属性值。
- Method类：表示类的方法，用于获取方法的描述信息或执行某个方法。
- Constructor类：表示类的构造方法。

### 2.3.5 反射的步骤

1. 获取想要操作的类的Class对象，该Class对象是反射的核心，通过它可以调用类的任意方法。
2. 调用Class对象所对应的类中定义的方法，这是反射的使用阶段。
3. 使用反射API来获取并调用类的属性和方法等信息。

获取Class对象的3种方法如下：

- 调用某个对象的getClass方法以获取该类对应的Class对象：

  ```java
  Person p = new Person();
  Class clazz = p.getClass();
  ```

- 调用某个类的class属性以获取该类对应的Class对象：

  ```java
  Class clazz = Person.class;
  ```

- 调用Class类中的forName静态方法以获取该类对应的Class对象，这是最安全、性能也最好的方法。

  ```java
  Class clazz = Class.forName("fullClassPath");
  // fullClassPath为类的包路径及名称
  ```

### 2.3.6 实例

```java
//1：获取Person类的Class对象
Class clazz = Class.forName("hello.java.reflect.Person");
//2：获取Person类的所有方法的信息
Method[] method = clazz.getDeclaredFieldsMethods();
for (Method m : method) {
    System.out.println(m.toString);
}
//3：获取Person类的所有成员的属性信息
Field[] field = clazz.getDeclareFields();
for (Field f : field) {
    System.out.println(f.toString());
}
//4：获取Person类的所有构造方法信息
Constructor[] constructor = clazz.getDeclaredConstructors();
for (Constructor c : constructor) {
    System.out.println(c.toString);
}
```



## 2.4 注解

## 2.5 内部类

## 2.6 泛型

泛型本质是参数化类型，泛型提供了编译时类型的安全检测机制，该机制允许程序再编译时检测非法的类型，比如要实现一个能够对字符串（String）、整形（Int）、浮点型（Float）、对象（Object）进行大小比较的方法，就可以使用Java泛型。

使用泛型的好处是在编译期就能检查类型是否安全，同时强制所有强制性类型转换都是自动和隐式进行的，提高了代码的安全性和重用性。

### 2.6.1 泛型标记和泛型限定：E、T、K、V、N、？

| 序号 | 泛型标记  | 说明                                     |
| ---- | --------- | ---------------------------------------- |
| 1    | E-Element | 在集合中使用，表示在集合中存放的元素     |
| 2    | T-Type    | 表示Java类，包括基本的类和我们自定义的类 |
| 3    | K-Key     | 表示键，比如Map中的key                   |
| 4    | V-Value   | 表示值                                   |
| 5    | N-Number  | 表示数值类型                             |
| 6    | ？        | 表示不确定的Java类型                     |

类型通配符“？”表示所有具体的参数类型，例如List<?>在逻辑上是List\<String>、List\<Integer>等所有List\<具体类型实参>的父类。

在使用泛型时，若希望将类的继承关系加入泛型应用中，就需要对泛型做限定，具体的泛型限定有对泛型上限的限定和泛型下限的限定。

**1. 对泛型上限的限定：\<? extends T>**

在java中使用通配符“？”和“extends”关键字指定泛型的上限，具体用法为<? extends T>，它表示该通配符所代表的类型是T类的子类或者接口T的子接口。

**2. 对泛型下限的限定：\<? super T>**

在java中使用通配符“？”和“super”关键字指定泛型的下限，具体用法为<? super T>，它表示该通配符所代表的类型是T类的子类或者接口T的父类或父接口。

### 2.6.2 泛型方法

泛型方法指将方法的参数类型定义为泛型，以便在调用时接收不同类型的参数。在方法的内部根据传递给泛型方法的不同参数类型执行不同的处理方法，具体用法如下：

```java
public static void main(String[] args) {
    generalMethod("1", 2, Worker{});
}

//定义泛型方法generalMethod，printArray为泛型参数列表
public static <T> void generalMethod(T ... inputArray) {
    for (T element : inputArray) {
        if (elemnt instanceof Integer) {
            System.out.println("处理Integer类型数据中...");
        } else if (elemnt instanceof String) {
            System.out.println("处理String类型数据中...");
        } else if (elemnt instanceof Double) {
            System.out.println("处理Double类型数据中...");
        } else if (elemnt instanceof Float) {
            System.out.println("处理Float类型数据中...");
        } else if (elemnt instanceof Long) {
            System.out.println("处理Long类型数据中...");
        } else if (elemnt instanceof Boolean) {
            System.out.println("处理Boolean类型数据中...");
        } else if (elemnt instanceof Date) {
            System.out.println("处理Date类型数据中...");
        } else if (elemnt instanceof Worker) {
            System.out.println("处理Worker类型数据中...");
        }
    }
}
```



### 2.6.3 泛型类

泛型类指在定义类时在类上定义了泛型，以便类在使用时可用根据传入的不同参数类型实例化不同的对象。

泛型类的具体使用方法是在类的名称后面添加一个或多个类型参数的声明部分，在多个泛型参数之间用逗号隔开。具体用法如下：

```java
//定义一个泛型类
public class GeneralClass<T> {
    public static void main(String[] args) {
        //根据需求初始化不同的类型
        GeneralClass<Integer> genInt = new GeneralClass<Integer>();
        genInt.add(1);
        GeneralClass<String> genStr = new GeneralClass<String>();
        genStr.add("2");
    }
    private T t;
    public void add(T t) {
        this.t = t;
    }
    public T get() {
        return t;
    }
}
```

实例化该类是为：`new GeneralClass<String>()`。

### 2.6.4 泛型接口

泛型接口的声明和泛型类的声明类似，通过在接口名后面添加类型参数的声明部分来实现。泛型接口的具体类型一般在实现类中进行声明，不同类型的实现类处理不同的业务逻辑。具体的实现代码如下：

```java
//定义一个泛型接口
public interface IGeneral<T> {
    public T getId();
}
//实现泛型接口的实现类
public class GeneralIntegerImpl implements IGeneral<Integer> {
    @Override
    public Integer getId() {
        Random random = new Random(100);
        return random.nextInt();
    }
}
public static void main(String[] args) {
    //使用泛型
    GeneralIntegerImpl gen = new GeneralIntegerImpl();
    System.out.println(gen.getId());
}
```



### 2.6.5 类型擦除

在编码阶段使用泛型时加上的类型参数，会被编译器在编译时去掉，这个过程被称为类型擦除。因此，泛型主要用于编译阶段。在编译后生成的Java字节代码文件中不包含泛型中的类型信息。例如，编码时定义的`List<Integer>`和`List<String>`在经过编译后统一为List。JVM所读取的只是List，由泛型附加的类型信息对JVM是不可见的。

Java类型的擦除过程为：

- 首先，查找用来替换类型参数的具体类（该具体类一般为Object），如果指定了类型参数的上界，则以该上界作为替换时的具体类；
- 然后，把代码中的类型参数都替换为具体的类。

## 2.7 序列化

